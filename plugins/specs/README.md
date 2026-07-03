# specs

A Claude Code plugin for authoring and reviewing software specifications in a traceable
**SPEC → feature → task** flow (with governing ADRs). A spec captures a capability at the
*what* level; each buildable slice becomes a feature where design lives; each feature is cut
into small tasks.

- **Skill** — `specs:create-spec`, authors a new `SPEC-NNNN` describing a capability at the
  *what* level with testable acceptance criteria, kept broad and stable so it can spawn many
  features.
- **Skill** — `specs:create-feature`, authors a new `FEAT-NNNN` — a buildable slice of a spec
  where design decisions live — broken into a sequence of tasks. Stops and proposes a spec (or
  an ADR) first if the work is untraced.
- **Skill** — `specs:create-task`, authors `TASK-NNNN` files under `docs/tasks/FEAT-NNNN/`, the
  smallest self-contained slice of a feature, tracing up to a spec and recording ordering in
  `depends_on:`.
- **Skill** — `specs:specs-review`, analyzes a specification against a structured checklist
  (level of abstraction, requirements quality, testable acceptance criteria, breadth &
  stability, traceability, and format/conventions) and reports actionable feedback — checking a
  spec stays at the *what* level and pushes design, sequencing, and decisions down to features,
  tasks, and ADRs.

## Install

```
/plugin marketplace add earelin/claude
/plugin install specs@earelin-plugins
```

## Usage

- "Write a spec for …" / "draft a new spec" → `create-spec`.
- "Turn this into a feature" / "draft FEAT-… for this spec" → `create-feature`.
- "Break this feature into tasks" / "create the tasks for FEAT-…" → `create-task`.
- "Review this specification" (paste or point to the spec) → `specs-review`.

## Structure

```
specs/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
└── skills/
    ├── create-spec/
    │   └── SKILL.md         # author a SPEC-NNNN
    ├── create-feature/
    │   └── SKILL.md         # author a FEAT-NNNN
    ├── create-task/
    │   └── SKILL.md         # author TASK-NNNN files
    └── specs-review/
        └── SKILL.md         # review a specification
```
