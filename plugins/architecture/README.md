# architecture

A Claude Code plugin for reviewing software architecture and design, and for authoring and
reviewing Architecture Decision Records (ADRs).

- **Skill** — `architecture:architecture-review`, a model-invoked skill that analyzes an
  architecture or design against a structured checklist (design & architecture, technology &
  tools, judgment & experience) and reports actionable feedback.
- **Skill** — `architecture:create-adr`, authors a new ADR (`NNNN`) capturing one
  architecturally significant decision in Nygard format, with correct sequential numbering and
  supersession handling.
- **Agent** — `adr-review`, a read-only subagent that reviews an ADR for significance, a
  plainly-stated decision, honest consequences, traceability, and correct
  immutability/supersession.

## Install

```
/plugin marketplace add earelin/claude
/plugin install architecture@earelin-plugins
```

## Usage

- "Review this architecture" / "review this design" → `architecture-review`.
- "Record this decision as an ADR" / "propose an ADR for …" → `create-adr`.
- "Review this ADR" / "is this decision record sound?" → `adr-review`.

## Structure

```
architecture/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
├── agents/
│   └── adr-review.md        # review an ADR (NNNN)
└── skills/
    ├── architecture-review/
    │   └── SKILL.md         # review an architecture or design
    └── create-adr/
        └── SKILL.md         # author an ADR (NNNN)
```
