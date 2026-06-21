# backend

A Claude Code plugin for reviewing backend pull requests and code changes.

- **Skill** — `backend:backend-review`, a model-invoked skill that analyzes a backend pull
  request against a structured checklist (code structure, data structures, design smells,
  software architecture, naming & formatting, comments, concurrency & performance, security,
  testing, API & contracts, data & persistence, error handling, observability, dependencies,
  and change scope) and reports actionable feedback.

## Install

```
/plugin marketplace add earelin/claude
/plugin install backend@earelin-plugins
```

## Usage

Ask Claude to "review this pull request" or "review these changes" (paste or point to the
diff) to trigger the `backend-review` skill.

## Structure

```
backend/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
└── skills/
    └── backend-review/
        └── SKILL.md         # model-invoked skill
```
