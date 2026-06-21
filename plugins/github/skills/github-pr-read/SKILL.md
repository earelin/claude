---
name: github-pr-read
description: Read a GitHub pull request including its description and all comments using the gh CLI. Use when the user asks to read, view, summarize, or look at a PR.
---

# GitHub PR Read skill

Fetch a pull request's full context — title, description, state, and every comment — using
the `gh` CLI, then summarize it for the user.

## Resolving the PR

The user may give a PR number, a URL, or nothing. `gh` accepts a number or URL as the
argument, and defaults to the PR for the current branch when none is given.

- Target a different repository with `-R owner/repo`.

## Commands

1. **Description + conversation comments** (the common case):

   ```
   gh pr view <pr> --comments
   ```

   This prints the title, body, state, and the conversation timeline (general/"issue"
   comments).

2. **Full coverage** — `--comments` does **not** include inline code-review comments or the
   review summaries (approve/request-changes). To capture everything:

   ```
   gh pr view <pr> --json title,body,state,author,url,comments,reviews
   ```

   And for inline review-thread comments on specific lines of the diff:

   ```
   gh api repos/{owner}/{repo}/pulls/<pr>/comments
   ```

   (Replace `{owner}/{repo}` with the target repo, or derive it from the PR URL.)

## Producing the summary

Read everything before summarizing. Report:

1. Title, PR number, state, author, and base ← head branches.
2. The description (body), condensed if long.
3. Comments grouped and in chronological order — distinguish review summaries and inline
   code-review threads from general conversation comments.
4. Call out anything that needs action: requested changes, unresolved threads, open
   questions.
