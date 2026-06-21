---
name: github-pr-create
description: Create a new GitHub pull request from the current branch using the gh CLI. Use when the user asks to open or create a PR.
---

# GitHub PR Create skill

Open a new pull request with the `gh` CLI.

## Preconditions

1. **Commit your work** — the PR reflects what's pushed, so ensure changes are committed.
2. **Never open a PR from the default branch** (`main`/`trunk`). If you're on it, create a
   feature branch first.
3. **Push the branch** so the remote has it:

   ```
   git push -u origin HEAD
   ```

4. Gather a clear **title** and **body**. If the user was vague, draft them and confirm the
   wording first — a PR is outward-facing.

## Command

```
gh pr create --title "..." --body "..."
```

Optional flags:

- `--base <branch>` — target branch to merge into (defaults to the repo default branch).
- `--draft` — open as a draft PR.
- `--reviewer <user>` — request reviewers (repeatable).
- `--label <name>` — apply labels (repeatable).
- `--fill` — derive the title and body from the branch's commits (handy when commits are
  already well-written).
- `-R owner/repo` — target a specific repository.

For long or multi-line bodies, use `--body-file <path>` (or `--body-file -` for stdin)
instead of `--body`, to avoid shell-quoting issues.

> Interactive mode (`-i`) is not supported in this environment — always pass `--title` and
> `--body`/`--body-file` (or use `--fill`) explicitly.

## After creating

`gh` prints the new PR's URL. Report it back to the user.
