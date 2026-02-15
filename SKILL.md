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
              "id": "apt-gh",
              "kind": "apt",
              "package": "gh",
              "bins": ["gh"],
              "label": "Install GitHub CLI (apt)",
            },
            {
              "id": "brew-jq",
              "kind": "brew",
              "formula": "jq",
              "bins": ["jq"],
              "label": "Install jq (brew)",
            },
            {
              "id": "apt-jq",
              "kind": "apt",
              "package": "jq",
              "bins": ["jq"],
              "label": "Install jq (apt)",
            },
          ],
      },
  }
---

# GitHub Star Manager

Requires `gh` (authenticated) + `jq`. For Lists operations: token needs `user` scope (Classic token recommended).

## Export Stars

```bash
gh api user/starred --paginate --jq '.[] | {
  full_name, description, url: .html_url, language,
  topics, stars: .stargazers_count, forks: .forks_count,
  archived, updated_at, pushed_at
}' | jq -s '.' > /tmp/stars.json
```

Slow for 1000+ stars (~1 min per 1000). Quick count: `gh api user/starred --paginate --jq '. | length' | jq -s 'add'`.

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

Save the returned `id` for adding repos.

## Add Repo to List

```bash
REPO_ID=$(gh api repos/OWNER/REPO --jq '.node_id')

gh api graphql -f query='
mutation($listId: ID!, $repoId: ID!) {
  addUserListItems(input: {listId: $listId, itemIds: [$repoId]}) {
    list { name items { totalCount } }
  }
}' -f listId="LIST_ID" -f repoId="$REPO_ID"
```

Add ~200ms delay between calls to avoid rate limits.

## Unstar

```bash
gh api -X DELETE user/starred/OWNER/REPO
```

Always confirm with user before unstarring.

## Organize Workflow

1. Export stars to JSON
2. Analyze the JSON — suggest categories based on language, topics, purpose, domain
3. Present suggestions to user for review
4. Create Lists and add repos after confirmation
5. Batch operations with delays between API calls

## Cleanup Workflow

1. Export stars to JSON
2. Use jq to find archived, stale (2+ years no push), low-star, or deprecated repos
3. Present candidates to user, confirm before unstarring
