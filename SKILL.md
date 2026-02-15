---
name: github-star-manager
description: Manage GitHub stars with AI-powered categorization and cleanup. Use when a user wants to organize their starred repos into GitHub Lists, clean up stale/deprecated stars, export star data for analysis, or get stats about their GitHub stars. Supports semantic categorization via LLM and bulk operations (unstar, add-to-list).
metadata:
  {
    "openclaw":
      {
        "emoji": "⭐",
        "requires": { "bins": ["gh", "jq"] },
        "install":
          [
            {
              "id": "brew-gh",
              "kind": "brew",
              "formula": "gh",
              "bins": ["gh"],
              "label": "Install GitHub CLI (brew)",
            },
            {
              "id": "brew-jq",
              "kind": "brew",
              "formula": "jq",
              "bins": ["jq"],
              "label": "Install jq (brew)",
            },
          ],
      },
  }
---

# GitHub Star Manager

Organize and clean up GitHub stars. The agent acts as the AI analysis layer — no external API keys needed.

Only dependency: `gh` (authenticated) + `jq`. Both are cross-platform (macOS/Linux/Windows).

For Lists operations (create/add): GitHub token needs `user` scope (Classic token recommended).

## Export Stars

```bash
gh api user/starred --paginate --jq '.[] | {
  full_name, description, url: .html_url, language,
  topics, stars: .stargazers_count, forks: .forks_count,
  archived, updated_at, pushed_at
}' | jq -s '.' > /tmp/stars.json
```

Slow for 1000+ stars (~1 min per 1000). For quick count: `gh api user/starred --paginate --jq '. | length' | jq -s 'add'`.

Each entry: `full_name`, `description`, `language`, `topics`, `stars`, `forks`, `archived`, `updated_at`, `pushed_at`.

## Quick Stats

```bash
# Total
jq 'length' /tmp/stars.json

# Top languages
jq '[.[].language // "None"] | group_by(.) | map({lang: .[0], n: length}) | sort_by(-.n) | .[:10]' /tmp/stars.json

# Most starred
jq -r 'sort_by(-.stars) | .[:10] | .[] | "\(.stars)\t\(.full_name)"' /tmp/stars.json

# Archived
jq '[.[] | select(.archived)] | .[].full_name' /tmp/stars.json
```

## View Existing Lists

```bash
gh api graphql -f query='{
  viewer {
    lists(first: 100) {
      nodes { id name description isPrivate items { totalCount } }
    }
  }
}' --jq '.data.viewer.lists.nodes'
```

## Create a List

```bash
gh api graphql -f query='
mutation($name: String!, $desc: String) {
  createUserList(input: {name: $name, description: $desc, isPrivate: false}) {
    list { id name }
  }
}' -f name="LIST_NAME" -f desc="LIST_DESCRIPTION" --jq '.data.createUserList.list'
```

Returns `{ "id": "UL_xxx", "name": "..." }`. Save the `id` for adding repos.

## Add Repo to List

```bash
# Step 1: get repo node ID
REPO_ID=$(gh api repos/OWNER/REPO --jq '.node_id')

# Step 2: add to list
gh api graphql -f query='
mutation($listId: ID!, $repoId: ID!) {
  addUserListItems(input: {listId: $listId, itemIds: [$repoId]}) {
    list { name items { totalCount } }
  }
}' -f listId="LIST_ID" -f repoId="$REPO_ID"
```

Add a ~200ms delay between calls to avoid rate limits. Process in batches.

## Unstar a Repo

```bash
gh api -X DELETE user/starred/OWNER/REPO
```

Always confirm with the user before unstarring.

## Organize Workflow

1. **Export** stars to JSON
2. **Read** the JSON, suggest categories based on semantic understanding (language, topics, purpose, domain)
3. **Present** suggestions to user for review
4. **Create** Lists and **add** repos after user confirms
5. Batch operations with delays between API calls

## Cleanup Workflow

From exported JSON, find candidates:

```bash
# Stale: not updated in 2+ years (use platform-appropriate date)
jq '[.[] | select(.pushed_at < "2024-01-01" and .archived == false)]' /tmp/stars.json

# Low stars
jq '[.[] | select(.stars < 10)]' /tmp/stars.json
```

Show candidates to user, confirm before unstarring.
