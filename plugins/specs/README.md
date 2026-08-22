# specs

A Claude Code plugin for authoring and reviewing software specifications in a traceable
**SPEC → feature → task** flow (with governing ADRs). A spec captures a capability at the
*what* level; each buildable slice becomes a feature where design lives; each feature is cut
into small tasks.

- **Skill** — `specs:create-spec`, authors a new `SPEC-NNNN` describing a capability at the
  *what* level with testable acceptance criteria, kept broad and stable so it can spawn many
  features.
- **Skill** — `specs:create-feature`, authors a new `FEAT-NNNN` — a buildable slice of a spec
  where design decisions live — as a folder under `docs/features/` whose `README.md` holds the
  description, broken into a sequence of tasks. Stops and proposes a spec (or an ADR) first if
  the work is untraced.
- **Skill** — `specs:create-task`, authors `TASK-NNNN` files inside the feature folder
  `docs/features/FEAT-NNNN-kebab-title/`, the smallest self-contained slice of a feature, tracing
  up to a spec and recording ordering in `depends_on:`.
- **Skill** — `specs:distill-feature`, distills a completed `FEAT-NNNN` into durable
  documentation — ADRs for decisions, module/architecture docs for current behaviour, user guides
  for user-visible behaviour, executable artifacts for rules — verifies nothing was lost with a
  round-trip test, then retires the feature and its task files.
- **Agent** — `specs-review`, a read-only subagent that reviews a `SPEC-NNNN` against a
  structured checklist (level of abstraction, requirements quality, testable acceptance
  criteria, breadth & stability, traceability, and format/conventions) — checking a spec stays
  at the *what* level and is ready to spawn features. Delegates to a `feature-review`
  subagent for every feature that already cites the spec, and factors their verdicts in.
- **Agent** — `feature-review`, a read-only subagent that reviews a `FEAT-NNNN` for design
  quality, traceability to its spec and ADRs, a genuine slice scope, and a sound breakdown into
  small tasks. Delegates to a `task-review` subagent for every task already cut from the
  feature, and factors their verdicts in.
- **Agent** — `task-review`, a read-only subagent that reviews a `TASK-NNNN` for small
  self-contained scope, traceability up to its feature and spec, testable acceptance criteria,
  and correct `depends_on:` ordering.

## Install

```
/plugin marketplace add earelin/claude
/plugin install specs@earelin-plugins
```

## Usage

- "Write a spec for …" / "draft a new spec" → `create-spec`.
- "Turn this into a feature" / "draft FEAT-… for this spec" → `create-feature`.
- "Break this feature into tasks" / "create the tasks for FEAT-…" → `create-task`.
- "This feature is shipped, close it out" / "clean up the finished design docs" → `distill-feature`.
- "Review this specification" (paste or point to the spec) → `specs-review`.
- "Review this feature" / "is FEAT-… ready to build?" → `feature-review`.
- "Review this task" / "is TASK-… ready to pick up?" → `task-review`.

## Structure

```
specs/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
├── agents/
│   ├── specs-review.md      # review a SPEC-NNNN
│   ├── feature-review.md    # review a FEAT-NNNN
│   └── task-review.md       # review a TASK-NNNN
└── skills/
    ├── create-spec/
    │   └── SKILL.md         # author a SPEC-NNNN
    ├── create-feature/
    │   └── SKILL.md         # author a FEAT-NNNN
    ├── create-task/
    │   └── SKILL.md         # author TASK-NNNN files
    └── distill-feature/
        └── SKILL.md         # retire a shipped FEAT-NNNN into durable docs
```
