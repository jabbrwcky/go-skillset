---
name: github-ci
description: CI/CD conventions for repositories hosted on GitHub, with Go-specific tooling. Use GitHub Actions for CI and other workflows, always pin actions to their latest available version, provide a Renovate configuration for automatically updating dependencies, Docker images, and workflow actions, run tests plus govulncheck and gosec security scans as PR checks with code coverage enforced above 75%, and sign images and binaries with cosign using keyless signing where possible. Always apply this whenever setting up CI, writing or updating GitHub Actions workflows, or preparing a Go project for release.
---

# GitHub CI Conventions

Applies whenever the repository lives on GitHub.

## CI platform

- Use **GitHub Actions** for CI and any other repository automation/workflows.
- Always use the **latest available version** of any action referenced in a workflow — check for newer major/minor versions rather than defaulting to whatever was scaffolded previously.

## Dependency updates

- Provide a **Renovate** configuration (`renovate.json`) that keeps dependencies, Docker images, and GitHub Actions versions automatically updated.

## PR checks

On every pull request, run as checks:

- **Tests** for the changed code.
- **`govulncheck`** — vulnerability scanning for Go.
- **`gosec`** — static security analysis for Go.
- **Code coverage gate**: fail the check if coverage drops below **75%**.

## Signing

- Sign built images and binaries with **cosign**, using **keyless signing**, wherever the artifact type supports it.
