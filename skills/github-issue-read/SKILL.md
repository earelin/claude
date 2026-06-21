---
name: github-issue-read
description: Read a GitHub issue including its description and all comments using the gh CLI. Use when the user asks to read, view, or summarize an issue.
---

# GitHub Issue Read skill

Fetch a GitHub issue's full context — title, description, metadata, and every comment —
using the `gh` CLI, then summarize it for the user.

## Resolving the issue

The user may give an issue number or a URL. Target a different repository with
`-R owner/repo`.

## Commands

1. **Description + comments** (the common case):

   ```
   gh issue view <issue> --comments
   ```

   This prints the title, body, state, and the full comment thread.

2. **Structured access** — when you need labels, assignees, or to process fields
   programmatically:

   ```
   gh issue view <issue> --json title,body,state,author,labels,assignees,comments,url
   ```

## Producing the summary

Read everything before summarizing. Report:

1. Title, issue number, state, author, labels, and assignees.
2. The description (body), condensed if long.
3. Comments in chronological order, attributing each to its author.
4. Call out anything actionable: decisions reached, open questions, requested follow-ups.
