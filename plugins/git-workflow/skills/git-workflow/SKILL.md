---
name: git-workflow
description: Git and GitHub workflow conventions covering commit message style, issue and PR writing style, worktree usage, branching, and Claude attribution. Use conventional commits, write compact issues/PRs that transport all necessary information, prefer git worktrees under .worktrees/ over switching branches in the current working directory, remove worktrees and stale branches once their PR is merged, use feature branches with pull requests, and ask the user once at the start of a project whether Claude attribution should be added to commits/issues/PRs. Always apply this whenever committing, branching, opening an issue or pull request, starting new feature work, or cleaning up after a pull request is merged in a git repository.
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
- Put worktrees under `.worktrees/<branch-name>` at the repository root. Keep that folder out of git status via the local exclude file, not `.gitignore`:
  ```sh
  x=$(git rev-parse --git-path info/exclude)  # resolves correctly inside a worktree too
  grep -qxF '.worktrees/' "$x" || echo '.worktrees/' >> "$x"
  git worktree add .worktrees/<branch-name> -b <branch-name>
  ```
- Use feature branches with pull requests for merging work — don't commit directly to the main branch.

## Cleanup after merge

Once a PR is merged, remove its worktree and branch. Run this from the main checkout, since a worktree can't remove itself:

```sh
gh pr view <branch-name> --json state,headRefOid  # must be MERGED, headRefOid == local tip
git worktree remove .worktrees/<branch-name>
git branch -D <branch-name>   # -D because -d refuses squash/rebase-merged branches
git fetch --prune && git worktree prune
```

- Use `-D` only after `gh` confirms `MERGED`. If `headRefOid` differs from `git rev-parse <branch-name>`, the branch has unmerged commits: keep it and ask the user.
- If `git worktree remove` refuses because of uncommitted changes, stop and ask the user. Don't add `--force`.

## Claude attribution

- At the **start of a new project** (first time working in a given repo), ask the user whether Claude attribution should be added to commits, issues, and PRs, or omitted. Apply their answer consistently for the rest of that project; don't ask again unless they raise it.
