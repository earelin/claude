# specs

A Claude Code plugin for reviewing software specifications.

- **Skill** — `specs:specs-review`, a model-invoked skill that analyzes a specification
  against a structured checklist (design, quality attributes, requirements quality,
  technology and process, team and skills) and reports actionable feedback.

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
