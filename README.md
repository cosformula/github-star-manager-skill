# github-star-manager-skill

[![License](https://img.shields.io/github/license/cosformula/github-star-manager-skill)](./LICENSE)

An [OpenClaw](https://github.com/openclaw/openclaw) skill for managing GitHub stars with AI-powered categorization and cleanup.

The agent itself acts as the AI layer — no external LLM API keys needed. Only requires `gh` CLI and `jq`.

## Features

- **Export** — Dump all starred repos to JSON/CSV for analysis
- **Stats** — Language breakdown, top repos, archived/stale counts
- **Organize** — AI-powered semantic categorization into GitHub Lists
- **Clean up** — Find and remove deprecated, archived, or stale stars
- **Cross-platform** — Works on macOS, Linux, and Windows (via `gh` + `jq`)

## Install

### Via ClawHub (recommended)

```bash
npm i -g clawhub
clawhub install github-star-manager-skill
```

### Manual

```bash
git clone https://github.com/cosformula/github-star-manager-skill.git skills/github-star-manager
```

## Prerequisites

- [`gh`](https://cli.github.com/) — GitHub CLI, authenticated (`gh auth login`)
- [`jq`](https://jqlang.github.io/jq/) — JSON processor
- For Lists operations (create/add): GitHub token needs `user` scope ([Classic token](https://github.com/settings/tokens) recommended)

## Usage

Just ask your OpenClaw agent:

- "Organize my GitHub stars"
- "How many stars do I have?"
- "Clean up stale starred repos"
- "Export my GitHub stars to JSON"
- "What languages are in my starred repos?"

The agent reads SKILL.md and handles everything — fetching data, analyzing repos, suggesting categories, and executing operations via `gh api`.

## How It Works

Unlike traditional tools that call external LLMs for analysis, this skill leverages the fact that OpenClaw agents **are** LLMs. The agent:

1. Exports starred repos via `gh api` (paginated)
2. Reads and semantically analyzes the repo data
3. Suggests meaningful categories based on language, topics, and purpose
4. Creates GitHub Lists and organizes repos after user confirmation

No OpenRouter key, no extra API costs — just `gh` and `jq`.

## Related

- [github-star-manager](https://github.com/cosformula/github-star-manager) — Standalone CLI version (Node.js + OpenRouter)

## License

MIT
