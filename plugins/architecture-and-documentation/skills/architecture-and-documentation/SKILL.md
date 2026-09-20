---
name: architecture-and-documentation
description: Architecture decision recording and documentation-style conventions for any software project. Record architectural decisions as Architectural Decision Records (ADRs) following https://adr.github.io/, keep all writing (ADRs, extended docs, commit messages, issues, PR descriptions) concise but sufficient for humans to understand, and put extended architecture and user documentation in a docs/ folder. Always apply this whenever making or discussing an architectural decision, writing or updating project documentation, or drafting a commit message, issue, or pull request description.
---

# Architecture & Documentation Conventions

## Architectural Decision Records

- Any decision that qualifies as an architectural decision must be recorded as an ADR following the format at [adr.github.io](https://adr.github.io/).
- Store ADRs under `docs/` (e.g. `docs/adr/0001-short-title.md`), numbered sequentially.
- When you make or the user approves an architectural decision during a task, proactively propose adding/updating the corresponding ADR rather than waiting to be asked.

## Writing style (applies everywhere: ADRs, docs, commits, issues, PRs)

- Be **concise but sufficient** — a human reading it should fully understand the decision or change without extra digging.
- Avoid repetition and avoid overly long explanations. State it once, clearly.
- Don't pad for length or restate the same point in different words.

## Documentation layout

- Extended architecture documentation (beyond the ADRs themselves) and user-facing documentation both live in a `docs/` folder at the repository root.
- Keep in-code comments and docstrings focused on the "why," and link out to `docs/` for anything that needs more than a couple of sentences.
