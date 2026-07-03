# frontend

A Claude Code plugin for reviewing and refactoring frontend pull requests and code changes.

- **Agent** — `code-reviewer`, a principal-level reviewer for frontend code changes in any
  language or framework. It scopes the diff, reads the project conventions, scores every
  issue for confidence, and reports only high-confidence findings (component logic, state &
  data flow, rendering & performance, accessibility, security, UX & contracts). Read-only —
  it never edits files.
- **Agent** — `refactoring`, a principal frontend engineer that iteratively improves the
  internal structure of the code a PR or branch adds or modifies over small,
  behaviour-preserving passes. It keeps rendered output, component API, and user-facing
  behaviour identical, gates every pass on a known-green safety net, caps at five passes,
  and prints a summary in the terminal.

## Install

```
/plugin marketplace add earelin/claude
/plugin install frontend@earelin-plugins
```

## Usage

Ask Claude to "review this frontend pull request" or "review these UI changes" to invoke the
`code-reviewer` agent, or "refactor the frontend code of this branch" to invoke the
`refactoring` agent.

## Structure

```
frontend/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
└── agents/
    ├── code-reviewer.md     # read-only frontend reviewer
    └── refactoring.md       # behaviour-preserving frontend refactorer
```
