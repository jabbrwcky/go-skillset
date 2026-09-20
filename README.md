# go-skillset

Go development conventions, packaged as installable Claude skills:

| Skill | Covers |
|---|---|
| `go-conventions` | Language choice, stdlib-first, dependency policy |
| `go-web-interfaces` | templ, Tailwind, air |
| `go-cli-interfaces` | kong, YAML config, mango-kong, shell completions, XDG paths |
| `architecture-and-documentation` | ADRs, writing style, docs/ layout |
| `git-workflow` | Conventional commits, PR/issue style, worktrees, attribution |
| `service-observability` | Liveness/readiness probes, OTEL metrics |
| `github-ci` | GitHub Actions, Renovate, security scans, coverage gate, cosign |

Skills don't sync between Claude Code and Claude.ai, so there are two separate install paths below — both read from the same `SKILL.md` files.

## Install in Claude Code

1. Push this repository to GitHub (or any git host).
2. Register the marketplace:
   ```
   /plugin marketplace add <your-username>/go-skillset
   ```
   (For local testing before pushing: `/plugin marketplace add ./go-skillset`)
3. Install whichever skills you want — each is its own plugin, so you can pick a subset:
   ```
   /plugin install go-conventions@go-skillset
   /plugin install go-cli-interfaces@go-skillset
   /plugin install github-ci@go-skillset
   ...
   ```
4. To make a marketplace required for your team automatically, add it to your project's `.claude/settings.json` (`enabledPlugins` field) — see Anthropic's plugin-marketplaces docs for the exact syntax.

## Install in Claude.ai

Claude.ai has no public marketplace — skills are uploaded as a zip per account (or provisioned org-wide by a Team/Enterprise owner).

- **Individually**: grab the matching `.skill` file from `claude-ai-skills/` in this repo and upload it via **Settings → Capabilities → Skills → Upload skill**.
- **Team/Enterprise**: an Owner can upload the same `.skill` files in **Organization settings → Skills** to provision them for everyone, or turn on the sharing toggles to let people share skills with specific colleagues/groups.

## Updating a skill

Edit the relevant `plugins/<name>/skills/<name>/SKILL.md`, bump the `version` in that plugin's `plugin.json`, commit and push. Claude Code picks up updates the next time someone reinstalls or the marketplace is refreshed. For Claude.ai, re-zip the folder and re-upload (there's no auto-update path there).
