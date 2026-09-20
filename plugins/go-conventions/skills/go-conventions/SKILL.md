---
name: go-conventions
description: Core Go development conventions covering language choice and dependency policy. Use Go as the implementation language for new software. Prefer the Go standard library over third-party dependencies whenever it offers equivalent functionality, and prefer actively maintained packages over implementing functionality from scratch when a dependency is warranted. Always apply this whenever writing, planning, reviewing, or refactoring Go code, or whenever deciding whether to add a dependency versus using stdlib or writing custom code.
---

# Go Conventions

These are baseline rules for any Go project. Other skills (`go-web-interfaces`, `go-cli-interfaces`, `service-observability`, `github-ci`) build on top of this one.

## Language

Default to Go for implementing software unless the user specifies otherwise.

## Dependency policy

When a piece of functionality is needed, evaluate in this order:

1. **Standard library first.** If `stdlib` covers the need (even if a bit more verbose), use it instead of pulling in a dependency.
2. **Actively maintained package.** If stdlib doesn't cover it, prefer an existing, actively maintained package over hand-rolling the implementation.
3. **Implement from scratch** only when no suitable actively maintained package exists, or the need is trivial enough that a dependency would be overkill.

## Definition: "actively maintained"

Before adding a dependency, check:

- The repository has had **commits within the last year**.
- The repository is **not archived**.

If a candidate package fails either check, look for an actively maintained alternative before falling back to a custom implementation. Mention to the user when a dependency you're proposing is borderline (e.g. maintained but slow-moving) so they can weigh in.
