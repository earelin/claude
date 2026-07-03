---
name: task-review
description: Review a task (TASK-NNNN) for small self-contained scope, traceability up to its feature and spec, testable acceptance criteria, and correct dependencies. Use when the user asks for a review of a task, or wants to confirm a task is ready to pick up and commit.
---

# Task Review skill

Analyze a task document and provide structured, actionable feedback on its quality. A **task**
(`TASK-NNNN`, under `docs/tasks/FEAT-NNNN/`) is the smallest traceable unit of work — a small,
self-contained change committed straight to `trunk`. A good task traces up to a feature (and
through it to a spec), touches a bounded, clearly scoped surface, carries testable acceptance
criteria that cite the spec or feature, and records its ordering in `depends_on:` rather than
prose.

This skill reviews the **task document itself**. Judging the parent feature's design or the
spec's soundness is the job of the `feature-review` and `specs-review` skills.

Use the criteria below as a checklist. They are independent prompts, not sequential steps —
work through the ones relevant to the task under review (not every question applies to every
task).

## Review criteria

### Traceability

- Does `feat:` frontmatter name a real parent feature, and does that feature trace to a spec?
  If the task is untraced (no feature, or a feature with no spec), flag it — the feature/spec
  should exist first.
- Does `adrs:` cite the governing decisions the task must honour?
- Do the acceptance criteria trace back to the spec (e.g. `SPEC-NNNN #4`) or to a feature
  requirement?

### Size & self-containment

- Is this **one small change** that can be committed to `trunk` as a single coherent unit?
- Is it too big (should be split into multiple tasks) or too trivial (should be folded into
  another)?
- Can it land without dragging in unrelated changes?

### Scope clarity

- Is the **Scope** list concrete and bounded — clear about what the change touches and what it
  does not?
- Is the goal stated in a line, and are the limits (e.g. "domain only — no transport") explicit?

### Acceptance criteria

- Are the criteria testable and pass/fail without guessing?
- Do they pin the observable behaviour the change must produce, tracing to the spec or feature?

### Dependencies & status

- Does `depends_on:` list the tasks in this feature that must land first — and is ordering kept
  there, not in prose?
- Are there missing or circular dependencies?
- Is `status:` a valid value (`todo` | `in-progress` | `done`) and does it match reality?

### Format & conventions

- Filename follows `TASK-NNNN-kebab-title.md`, with `NNNN` sequential **within** the
  `docs/tasks/FEAT-NNNN/` folder.
- Frontmatter present: `feat:` (required), `adrs:` (if any), `status:`, `depends_on:`.
- Diagrams are Mermaid fenced blocks (never ASCII art); only folder/file trees use indented text
  blocks.

## Producing the review

1. Read the task thoroughly before judging it. Read its parent feature (especially the
   sequencing section), the spec criteria it cites, and skim the folder conventions
   (`docs/tasks/CLAUDE.md`) and any sibling tasks it depends on.
2. Work through the relevant criteria above, gathering observations for each category.
3. Report findings grouped by the categories above. For each finding, note its
   severity/priority and give a concrete, actionable suggestion — including whether the task
   should be split, merged, or re-scoped.
4. Close with an overall assessment of the task's quality and its readiness to be picked up: is
   it traced, small and self-contained, verifiable, and correctly ordered?
