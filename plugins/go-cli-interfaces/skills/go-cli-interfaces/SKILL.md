---
name: go-cli-interfaces
description: Stack conventions for building Go command-line tools. Default to kong for CLI argument parsing, YAML as the preferred config file format loaded by passing kong-yaml's loader to kong.Configuration() as a kong.Parse() option (never a hand-rolled config parser), mango-kong for man page generation, github.com/miekg/king for shell completions, kong.BindTo for injecting a context.Context into command Run methods, and honoring the XDG Base Directory Specification for config/data/cache file locations. Always apply this whenever building, scaffolding, or modifying a Go CLI tool — even if the user just says "build a CLI/command-line tool" without naming a stack.
---

# Go CLI Interfaces

Applies together with `go-conventions`. Use this whenever the deliverable is a Go command-line program.

## Stack defaults

- **CLI parsing**: [`alecthomas/kong`](https://github.com/alecthomas/kong) for flags, args, and subcommands.
- **Config file format**: YAML is the preferred default, loaded by [`alecthomas/kong-yaml`](https://github.com/alecthomas/kong-yaml). Use [`kong-toml`](https://github.com/alecthomas/kong-toml) or [`kong-hcl`](https://github.com/alecthomas/kong-hcl) only if the user asks for TOML or HCL specifically.
- **Config loading mechanism**: The loader is passed to kong as an *option*, via `kong.Configuration(loader, paths...)`. Never write config-file parsing, merging, precedence, or unmarshalling yourself.
- **Config file flag**: Use kong's built-in `kong.ConfigFlag` field type so `--config` feeds the same configured loader. Don't add a custom flag plus manual file reading.
- **Man pages**: Generate them with [`alecthomas/mango-kong`](https://github.com/alecthomas/mango-kong) as part of the build/release process, not written by hand.
- **Shell completions**: Generate them with `github.com/miekg/king`.
- **File locations**: Honor the [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir/latest/) for where the tool reads/writes config, data, and cache files (`$XDG_CONFIG_HOME`, `$XDG_DATA_HOME`, `$XDG_CACHE_HOME`, with their documented fallbacks) — don't invent custom dotfile locations.

## Config loading: use the kong loader, not your own

The config file is not a separate subsystem. It is a *resolver* for the flags and args already declared in the CLI struct: every config key corresponds to a flag, and kong applies file values underneath command-line values automatically.

So there is nothing to implement. In particular, do **not**:

- add `gopkg.in/yaml.v3`, `github.com/BurntSushi/toml`, `viper`, or similar to read the config file;
- declare a separate `Config` struct and unmarshal into it;
- write precedence logic for flag vs. file vs. environment (kong already resolves flag > env > config > default);
- read the `--config` path yourself before or after calling `kong.Parse`.

Each loader is a `kong.ConfigurationLoader` (`func(r io.Reader) (kong.Resolver, error)`), exposed as `Loader` by every one of these packages:

| Format | Import | Loader |
| --- | --- | --- |
| YAML (default) | `github.com/alecthomas/kong-yaml` | `kongyaml.Loader` |
| TOML | `github.com/alecthomas/kong-toml` | `kongtoml.Loader` |
| HCL | `github.com/alecthomas/kong-hcl` | `konghcl.Loader` |

## Canonical wiring

```go
package main

import (
	"context"
	"os"
	"os/signal"

	"github.com/alecthomas/kong"
	kongyaml "github.com/alecthomas/kong-yaml"
)

// Set at build time with -ldflags "-X main.version=...".
var version = "dev"

type CLI struct {
	Config  kong.ConfigFlag  `help:"Load configuration from a file." placeholder:"PATH"`
	Version kong.VersionFlag `help:"Show version and exit."`

	Verbose bool `help:"Enable verbose output." env:"MYTOOL_VERBOSE"`

	Serve ServeCmd `cmd:"" help:"Run the server."`
}

func main() {
	ctx, cancel := signal.NotifyContext(context.Background(), os.Interrupt)
	defer cancel()

	var cli CLI
	kctx := kong.Parse(&cli,
		kong.Name("mytool"),
		kong.Description("..."),
		// Config loader is an option to Parse. Paths are searched in order;
		// ~ and $VAR are expanded by kong. --config overrides these.
		kong.Configuration(kongyaml.Loader,
			"$XDG_CONFIG_HOME/mytool/config.yaml",
			"~/.config/mytool/config.yaml",
			"/etc/mytool/config.yaml",
		),
		kong.UsageOnError(),
		// context.Context is an interface, so it must be bound with BindTo.
		kong.BindTo(ctx, (*context.Context)(nil)),
		kong.Vars{"version": version},
	)

	kctx.FatalIfErrorf(kctx.Run())
}

type ServeCmd struct {
	Addr string `help:"Listen address." default:":8080"`
}

// ctx is injected by kong because of the BindTo above.
func (c *ServeCmd) Run(ctx context.Context) error {
	// ...
	return nil
}
```

## Binding a context.Context

Any command whose `Run` method takes a `context.Context` requires that context to be bound **to the interface type**:

```go
kong.BindTo(ctx, (*context.Context)(nil))
```

`kong.Bind(ctx)` is wrong here. Kong resolves `Run` parameters by the value's dynamic type, and a `context.Context` value's dynamic type is an unexported concrete type (e.g. `*context.cancelCtx`), which never matches the `context.Context` parameter. Use `kong.Bind` only for concrete types (your own `*Deps`, `*slog.Logger`, and so on).

Getting this wrong is a *runtime* failure, not a compile error, so it is easy to ship:

```
couldn't find binding of type context.Context for parameter 0 of
func(context.Context) error(), use kong.Bind(context.Context)
```

Note that kong's own hint in that message is misleading — `kong.Bind` is exactly what does not work. `kong.BindTo(ctx, (*context.Context)(nil))` is the fix.

The same rule applies to binding at `Run` time and inside hooks:

```go
kctx.Run(ctx)                                  // wrong — binds the concrete type
kctx.BindTo(ctx, (*context.Context)(nil))      // right
```

Prefer passing `kong.BindTo(...)` to `kong.Parse` so every command shares one context. For values that can only be constructed per-command, use `kong.BindToProvider`.

## Notes

- Default config search path should follow XDG conventions (e.g. `$XDG_CONFIG_HOME/<tool>/config.yaml`), with the explicit `--config` flag able to override it.
- Missing config files are not an error — kong skips paths it cannot open, so listing several candidates is safe.
- When scaffolding a new CLI, wire up man page and completion generation as build/release steps (see `github-ci`) rather than one-off manual commands.
