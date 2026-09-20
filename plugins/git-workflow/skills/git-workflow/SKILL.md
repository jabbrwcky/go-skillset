---
name: git-workflow
description: Git and GitHub workflow conventions covering commit message style, issue and PR writing style, worktree usage, branching, and Claude attribution. Use conventional commits, write compact issues/PRs that transport all necessary information, prefer git worktrees over switching branches in the current working directory, use feature branches with pull requests, and ask the user once at the start of a project whether Claude attribution should be added to commits/issues/PRs. Always apply this whenever committing, branching, opening an issue or pull request, or starting new feature work in a git repository.
---

# Git Workflow Conventions

## Commit messages

- Use [Conventional Commits](https://www.conventionalcommits.org/) format (`type(scope): subject`).
- Keep messages short and to the point — but never omit crucial information.
- If a change needs more explanation or feature documentation than fits in a short message, link to the relevant file in `docs/` (see `architecture-and-documentation`) instead of writing a long commit body.

## Issues and pull requests

- Use the same writing style as commits: compact, but transporting all necessary information. No filler, no repetition (see `architecture-and-documentation` for the general writing-style rule).

## Branching and worktrees

- When implementing or planning a feature, prefer creating a **git worktree** over switching branches in the current working directory, so multiple lines of work can stay checked out side by side.
- Use feature branches with pull requests for merging work — don't commit directly to the main branch.

## Claude attribution

- At the **start of a new project** (first time working in a given repo), ask the user whether Claude attribution should be added to commits, issues, and PRs, or omitted. Apply their answer consistently for the rest of that project; don't ask again unless they raise it.
