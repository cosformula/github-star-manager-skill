# github-star-manager-skill

[![ClawHub](https://img.shields.io/badge/ClawHub-github--star--manager--skill-blue?logo=data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMjQiIGhlaWdodD0iMjQiIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZD0iTTEyIDJMMTMuMDkgOC4yNkwyMCA5TDEzLjA5IDE1Ljc0TDEyIDIyTDEwLjkxIDE1Ljc0TDQgOUwxMC45MSA4LjI2TDEyIDJaIiBmaWxsPSJ3aGl0ZSIvPgo8L3N2Zz4K)](https://clawhub.ai/skills/github-star-manager-skill)
[![ClawHub version](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fclawhub.ai%2Fapi%2Fv1%2Fskills%2Fgithub-star-manager-skill&query=%24.skill.tags.latest&label=clawhub&prefix=v&color=blue)](https://clawhub.ai/skills/github-star-manager-skill)
[![ClawHub downloads](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fclawhub.ai%2Fapi%2Fv1%2Fskills%2Fgithub-star-manager-skill&query=%24.skill.stats.downloads&label=clawhub%20downloads&color=blue)](https://clawhub.ai/skills/github-star-manager-skill)
[![GitHub stars](https://img.shields.io/github/stars/cosformula/github-star-manager-skill?style=flat&logo=github)](https://github.com/cosformula/github-star-manager-skill)
[![License](https://img.shields.io/github/license/cosformula/github-star-manager-skill)](./LICENSE)
[![CI](https://github.com/cosformula/github-star-manager-skill/actions/workflows/ci.yml/badge.svg)](https://github.com/cosformula/github-star-manager-skill/actions/workflows/ci.yml)
[![Publish to ClawHub](https://github.com/cosformula/github-star-manager-skill/actions/workflows/clawhub-publish.yml/badge.svg)](https://github.com/cosformula/github-star-manager-skill/actions/workflows/clawhub-publish.yml)

An agent skill for managing GitHub stars with AI-powered categorization and cleanup.

The agent itself acts as the AI layer — no external LLM API keys needed. Only requires `gh` CLI and `jq`.

## Features

- **Export** — Dump all starred repos to JSON/CSV for analysis
- **Stats** — Language breakdown, top repos, archived/stale counts
- **Organize** — AI-powered semantic categorization into GitHub Lists
- **Clean up** — Find and remove deprecated, archived, or stale stars
- **Cross-platform** — Works on macOS, Linux, and Windows (via `gh` + `jq`)

## Install

### Via npx skills (any agent)

```bash
npx skills add cosformula/github-star-manager-skill
```

### Via ClawHub (OpenClaw)

```bash
clawhub install github-star-manager-skill
```

### Manual

```bash
git clone https://github.com/cosformula/github-star-manager-skill.git
```

## Prerequisites

- [`gh`](https://cli.github.com/) — GitHub CLI, authenticated (`gh auth login`)
- [`jq`](https://jqlang.github.io/jq/) — JSON processor
- For Lists operations (create/add): GitHub token needs `user` scope ([Classic token](https://github.com/settings/tokens) recommended)

## Usage

Just ask your agent:

- "Organize my GitHub stars"
- "How many stars do I have?"
- "Clean up stale starred repos"
- "Export my GitHub stars to JSON"
- "What languages are in my starred repos?"

The agent reads SKILL.md and handles everything — fetching data, analyzing repos, suggesting categories, and executing operations via `gh api`.

## How It Works

Unlike traditional tools that call external LLMs for analysis, this skill leverages the fact that coding agents **are** LLMs. The agent:

1. Exports starred repos via `gh api` (paginated)
2. Reads and semantically analyzes the repo data
3. Suggests meaningful categories based on language, topics, and purpose
4. Creates GitHub Lists and organizes repos after user confirmation

No OpenRouter key, no extra API costs — just `gh` and `jq`.

## Related

- [github-star-manager](https://github.com/cosformula/github-star-manager) — Standalone CLI version (Node.js + OpenRouter)

## License

MIT
