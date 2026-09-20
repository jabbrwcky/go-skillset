---
name: go-web-interfaces
description: Stack conventions for building Go web interfaces and web services with a UI. Default to templ for server-side templating, a CSS framework such as Tailwind CSS for styling, and air for live reload during local development. Always apply this whenever building, scaffolding, or modifying a Go web application, an HTTP handler that renders a UI, or a web frontend — even if the user just says "build a web page/dashboard/UI" without naming a stack.
---

# Go Web Interfaces

Applies together with `go-conventions`. Use this whenever the deliverable is a Go program with a web UI.

## Stack defaults

- **Templating**: [`a-h/templ`](https://github.com/a-h/templ) ([docs](https://templ.guide/)). Write views as `.templ` components, not raw `html/template` strings, unless the user asks otherwise.
- **CSS**: A utility CSS framework — Tailwind CSS by default — rather than hand-rolled stylesheets, unless the project already has an established style system.
- **Local dev loop**: [`air-verse/air`](https://github.com/air-verse/air) for live reload while developing. Set up an `.air.toml` so `air` rebuilds and restarts the server on file changes, including `.templ` regeneration.

## Project setup notes

- Make sure `templ generate` (or an equivalent air pre-build hook) runs before the binary builds, so `.templ` changes are picked up automatically during live reload.
- Keep templ components organized under a clear `views/` or `templates/` directory rather than scattering them alongside handlers.
- If the project needs a build pipeline for Tailwind (e.g. the Tailwind CLI or a bundler), wire it into the same `air` dev loop so a single command runs the whole stack.
