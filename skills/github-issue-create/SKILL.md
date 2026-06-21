---
name: github-issue-create
description: Create a new GitHub issue using the gh CLI. Use when the user asks to open, file, or create an issue.
---

# GitHub Issue Create skill

Open a new GitHub issue with the `gh` CLI.

## Before creating

1. Gather a clear **title** and a **body**. If the user was vague, draft them and confirm
   the wording first — creating an issue is outward-facing and visible to the whole repo.
2. Determine the target repo. By default `gh` uses the current repository; pass
   `-R owner/repo` to target another.

## Command

```
gh issue create --title "..." --body "..."
```

Optional flags:

- `--label <name>` — apply one or more labels (repeatable).
- `--assignee <user>` — assign people (use `@me` for yourself).
- `--milestone <name>` — attach to a milestone.
- `-R owner/repo` — target a specific repository.

For long or multi-line bodies, write the text to a file and use `--body-file <path>` (or
`--body-file -` to read from stdin) instead of `--body`, to avoid shell-quoting issues.

> Interactive mode (`-i`) is not supported in this environment — always pass `--title` and
> `--body`/`--body-file` explicitly.

## After creating

`gh` prints the new issue's URL. Report it back to the user.
