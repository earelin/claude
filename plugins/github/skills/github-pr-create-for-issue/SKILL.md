---
name: github-pr-create-for-issue
description: Create a GitHub pull request linked to an issue (so it auto-closes on merge) using the gh CLI. Use when the user asks to open a PR that addresses, fixes, or closes a specific issue.
---

# GitHub PR Create For Issue skill

Open a pull request that is linked to an existing issue, so GitHub auto-closes the issue
when the PR merges. This builds on the `github-pr-create` skill — the only difference is the
issue link in the body.

## 1. Identify the issue

Get the target issue number from the user. If helpful, read it first to ground the PR title
and description in the actual problem (see the `github-issue-read` skill):

```
gh issue view <issue> --json title,body,state,url
```

Confirm the issue is open and in the same repo you'll open the PR against.

## 2. Preconditions (same as github-pr-create)

1. **Commit your work.**
2. **Never open a PR from the default branch** (`main`/`trunk`); create a feature branch
   first.
3. **Push the branch:** `git push -u origin HEAD`.
4. Draft a clear **title** and **body**, and confirm the wording with the user — a PR is
   outward-facing.

## 3. Link the issue in the PR body

Put a **closing keyword** on its own line in the body so GitHub links and auto-closes the
issue on merge. Recognized keywords: `Closes`, `Fixes`, `Resolves` (+ `#<number>`).

```
Closes #<issue>
```

To close several issues, list each on its own line (`Closes #12`, `Closes #15`). To
reference an issue *without* closing it, mention `#<issue>` without a keyword.

> Note: closing keywords only auto-close when the PR targets the repo's **default branch**.
> If `--base` is a non-default branch, the link still appears but the issue won't auto-close
> on merge — call this out to the user.

## 4. Create the PR

```
gh pr create --title "..." --body "Closes #<issue>

<rest of the description>"
```

For longer bodies, write the text (including the `Closes #<issue>` line) to a file and use
`--body-file <path>`. Useful flags: `--base`, `--draft`, `--reviewer`, `--label`,
`-R owner/repo`. Interactive mode (`-i`) is unsupported here — always pass `--title` and
`--body`/`--body-file`.

## 5. After creating

`gh` prints the new PR's URL. Report it, and confirm the issue link is shown in the PR's
"Development"/linked-issues section.
