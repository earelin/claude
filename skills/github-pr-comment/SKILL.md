---
name: github-pr-comment
description: Add a comment to a GitHub pull request using the gh CLI. Use when the user asks to comment on, reply to, or leave a note on a PR.
---

# GitHub PR Comment skill

Post a comment to a pull request with the `gh` CLI.

## Resolving the PR

The user may give a PR number, a URL, or nothing. `gh` accepts a number or URL as the
argument, and defaults to the PR for the current branch when none is given.

- Target a different repository with `-R owner/repo`.

## Before commenting

Gather the comment text. If the user was vague, draft it and confirm the wording first — a
comment is outward-facing and visible to the whole repo.

## Command

```
gh pr comment <pr> --body "..."
```

Optional flags:

- `--body-file <path>` — read the body from a file (or `--body-file -` for stdin). Use this
  for long or multi-line comments to avoid shell-quoting issues.
- `--edit-last` — edit your most recent comment on the PR instead of adding a new one.
- `-R owner/repo` — target a specific repository.

> Interactive mode (`-i`) is not supported in this environment — always pass `--body` or
> `--body-file` explicitly.

## Inline review comments

`gh pr comment` posts a general conversation comment. To comment on a specific line of the
diff (an inline review comment), use the API:

```
gh api repos/{owner}/{repo}/pulls/<pr>/comments \
  -f body="..." \
  -f commit_id="<sha>" \
  -f path="<file>" \
  -F line=<n> \
  -f side=RIGHT
```

(Replace `{owner}/{repo}` with the target repo, or derive it from the PR URL. Get the head
`commit_id` from `gh pr view <pr> --json headRefOid`.)

## After commenting

`gh` prints the new comment's URL. Report it back to the user.
