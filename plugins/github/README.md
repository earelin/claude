# github

A Claude Code plugin that wraps common GitHub pull-request and issue workflows with the
`gh` CLI.

- **`github:github-pr-create`** — open a new pull request from the current branch.
- **`github:github-pr-create-for-issue`** — open a pull request linked to an issue (so it
  auto-closes on merge).
- **`github:github-pr-read`** — read a pull request, including its description and comments.
- **`github:github-pr-comment`** — add a comment to a pull request.
- **`github:github-pr-related-issue`** — find and read the issue(s) a pull request relates to.
- **`github:github-issue-create`** — open a new issue.
- **`github:github-issue-read`** — read an issue, including its description and comments.

## Install

```
/plugin marketplace add earelin/claude
/plugin install github@earelin-plugins
```

## Usage

Ask Claude to "open a PR", "read PR #123", "comment on this PR", "create an issue", or
"read issue #45" to trigger the matching skill. Each skill confirms wording before anything
outward-facing and never opens a PR from the default branch.

## Structure

```
github/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
└── skills/
    ├── github-issue-create/
    ├── github-issue-read/
    ├── github-pr-comment/
    ├── github-pr-create/
    ├── github-pr-create-for-issue/
    ├── github-pr-read/
    └── github-pr-related-issue/
        └── SKILL.md         # model-invoked skill
```
