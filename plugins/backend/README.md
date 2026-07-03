# backend

A Claude Code plugin for reviewing and refactoring backend pull requests and code changes.

- **Agent** — `code-reviewer`, a principal-level reviewer for backend code changes in any
  language or framework. It scopes the diff, reads the project conventions, scores every
  issue for confidence, and reports only high-confidence findings (correctness & business
  logic, architecture & boundaries, data & transactions, concurrency & scaling, security,
  API contract & testing). Read-only — it never edits files.
- **Agent** — `refactoring`, a principal backend engineer that iteratively improves the
  internal structure of the code a PR or branch adds or modifies over small,
  behaviour-preserving passes. It keeps the public API, data contract, and query semantics
  identical, gates every pass on a known-green safety net, caps at five passes, and prints a
  summary in the terminal.

## Install

```
/plugin marketplace add earelin/claude
/plugin install backend@earelin-plugins
```

## Usage

Ask Claude to "review this backend pull request" or "review these changes" to invoke the
`code-reviewer` agent, or "refactor the backend code of this branch" to invoke the
`refactoring` agent.

## Structure

```
backend/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
└── agents/
    ├── code-reviewer.md     # read-only backend reviewer
    └── refactoring.md       # behaviour-preserving backend refactorer
```
