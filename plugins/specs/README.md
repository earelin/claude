# specs

A Claude Code plugin for reviewing software specifications.

- **Skill** — `specs:specs-review`, a model-invoked skill that analyzes a specification
  against a structured checklist (level of abstraction, requirements quality, testable
  acceptance criteria, breadth & stability, traceability, and format/conventions) and reports
  actionable feedback. It reviews specs for fitness within a traceable **SPEC → feature → task**
  authoring flow (with governing ADRs) — checking a spec stays at the *what* level and pushes
  design, sequencing, and decisions down to features, tasks, and ADRs.

## Install

```
/plugin marketplace add earelin/claude
/plugin install specs@earelin-plugins
```

## Usage

Ask Claude to "review this specification" (paste or point to the spec) to trigger the
`specs-review` skill.

## Structure

```
specs/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
└── skills/
    └── specs-review/
        └── SKILL.md         # model-invoked skill
```
