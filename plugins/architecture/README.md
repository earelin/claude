# architecture

A Claude Code plugin for reviewing software architecture and design.

- **Skill** — `architecture:architecture-review`, a model-invoked skill that analyzes an
  architecture or design against a structured checklist (design & architecture, technology &
  tools, judgment & experience) and reports actionable feedback.

## Install

```
/plugin marketplace add earelin/claude
/plugin install architecture@earelin-plugins
```

## Usage

Ask Claude to "review this architecture" or "review this design" (paste or point to the
design) to trigger the `architecture-review` skill.

## Structure

```
architecture/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
└── skills/
    └── architecture-review/
        └── SKILL.md         # model-invoked skill
```
