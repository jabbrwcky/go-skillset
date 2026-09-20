---
name: go-cli-interfaces
description: Stack conventions for building Go command-line tools. Default to kong for CLI argument parsing, YAML as the preferred config file format loaded via kong's config-loader support, mango-kong for man page generation, github.com/miekg/king for shell completions, and honoring the XDG Base Directory Specification for config/data/cache file locations. Always apply this whenever building, scaffolding, or modifying a Go CLI tool — even if the user just says "build a CLI/command-line tool" without naming a stack.
---

# Go CLI Interfaces

Applies together with `go-conventions`. Use this whenever the deliverable is a Go command-line program.

## Stack defaults

- **CLI parsing**: [`alecthomas/kong`](https://github.com/alecthomas/kong) for flags, args, and subcommands.
- **Config file format**: YAML is the preferred default. Load it via [`alecthomas/kong-yaml`](https://github.com/alecthomas/kong-yaml). Fall back to [`kong-toml`](https://github.com/alecthomas/kong-toml) or [`kong-hcl`](https://github.com/alecthomas/kong-hcl) only if the user asks for TOML or HCL specifically.
- **Config file flag**: Use kong's built-in support for a flag that lets the user pass an explicit config file path on the command line (rather than a custom flag implementation).
- **Man pages**: Generate them with [`alecthomas/mango-kong`](https://github.com/alecthomas/mango-kong) as part of the build/release process, not written by hand.
- **Shell completions**: Generate them with `github.com/miekg/king`.
- **File locations**: Honor the [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir/latest/) for where the tool reads/writes config, data, and cache files (`$XDG_CONFIG_HOME`, `$XDG_DATA_HOME`, `$XDG_CACHE_HOME`, with their documented fallbacks) — don't invent custom dotfile locations.

## Notes

- Default config search path should follow XDG conventions (e.g. `$XDG_CONFIG_HOME/<tool>/config.yaml`), with the explicit `--config` flag able to override it.
- When scaffolding a new CLI, wire up man page and completion generation as build/release steps (see `github-ci`) rather than one-off manual commands.
