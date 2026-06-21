---
name: github-pr-related-issue
description: Find and read the GitHub issue(s) linked to a pull request using the gh CLI. Use when the user asks to read the issue a PR addresses, closes, or relates to.
---

# GitHub PR Related Issue skill

Given a pull request, discover which issue(s) it is linked to, then read those issues so
you can summarize the original problem the PR addresses.

## 1. Resolve the PR

The user may give a PR number, URL, or nothing (default to the PR for the current branch).
Target a different repository with `-R owner/repo`.

## 2. Find the linked issue(s)

A PR can be linked to issues two ways — check both:

1. **Closing references** (the reliable source) — issues GitHub will auto-close on merge,
   set via keywords (`Closes #12`, `Fixes #12`, `Resolves #12`) or the PR sidebar. Fetch
   them with the GraphQL API:

   ```
   gh api graphql -f query='
     query($owner:String!, $repo:String!, $pr:Int!) {
       repository(owner:$owner, name:$repo) {
         pullRequest(number:$pr) {
           closingIssuesReferences(first:20) {
             nodes { number title url state }
           }
         }
       }
     }' -F owner=<owner> -F repo=<repo> -F pr=<pr>
   ```

2. **Mentions in the PR body/comments** — issues referenced without a closing keyword.
   Read the PR body and scan for `#<number>` references that GraphQL did not return:

   ```
   gh pr view <pr> --json body,title
   ```

If no linked issue is found, tell the user — the PR may not reference one.

## 3. Read the issue(s)

For each linked issue number, read its description and comments:

```
gh issue view <issue> --comments
```

(or `--json title,body,state,author,labels,comments` for structured access). This mirrors
the `github-issue-read` skill — reuse that recipe.

## 4. Summarize

Report, for each linked issue: number, title, state, the problem it describes, and how it
relates to the PR (closes vs. merely references). Note when a PR closes multiple issues, or
references issues it does not close.
