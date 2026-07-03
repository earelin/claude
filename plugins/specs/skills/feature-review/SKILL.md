---
name: feature-review
description: Review a feature (FEAT-NNNN) for design quality, traceability to its spec and ADRs, and a sound breakdown into small tasks. Use when the user asks for a review of a feature, or wants to understand how ready a feature is to be broken into tasks and built.
---

# Feature Review skill

Analyze a feature document and provide structured, actionable feedback on its quality. A
**feature** (`FEAT-NNNN`) is a buildable slice of a spec — it is where design decisions live,
and it enumerates the small tasks that implement it. A good feature traces up to a spec (and any
governing ADRs), is a genuine slice rather than the whole spec, carries the implementation
detail the spec deliberately omits, and decomposes cleanly into small, self-contained tasks.

This skill reviews the **feature document itself**. Judging whether the parent spec is sound is
the `specs-review` skill's job; judging an individual task's scope is `task-review`'s.

Use the criteria below as a checklist. They are independent prompts, not sequential steps —
work through the ones relevant to the feature under review (not every question applies to every
feature).

## Review criteria

### Traceability & framing

- Does `spec:` frontmatter name a real parent spec, and does the Goal link it?
- Does `adrs:` list the governing ADRs, and does the design reference them where decisions bite?
- Is there an implied architecturally significant decision (a new module or boundary, a datastore
  or public-contract change, a cross-cutting pattern) with **no ADR** behind it? Flag it — the
  ADR should exist before the feature builds against it.

### Slice scope

- Is this a buildable *slice* of the spec, not an attempt to swallow the whole spec at once?
- Is what's **out of scope** stated explicitly, naming the separate features that own the rest?
- Is the slice small enough to build coherently, yet complete enough to deliver value?

### Design quality

- Does the design carry the detail the spec omits — components, contracts, sequencing, edge
  cases — at the right level?
- Is the design internally consistent and consistent with the cited ADRs?
- Are contracts (APIs, schemas, events) and their boundaries defined clearly enough to build from?

### Task breakdown

- Does the sequencing section enumerate small, self-contained tasks, each a single coherent
  change?
- Is the ordering sound (dependencies flow forward), and does each task cite the spec criteria it
  satisfies (e.g. `SPEC-NNNN #3`)?
- Together, do the tasks cover every spec acceptance criterion this feature claims — nothing
  dropped, nothing built that no criterion asks for?

### Edge cases

- Are the tricky conditions identified, and does each trace to a spec criterion or an explicit
  design decision?
- Are failure and partial-failure paths considered, not just the happy path?

### Format & conventions

- Filename follows `FEAT-NNNN-kebab-title.md` with a sequential `NNNN`.
- Frontmatter present: `spec:` (required), `adrs:` (if any), valid `status:` (`draft` | `active`
  | `implemented`).
- Diagrams are Mermaid fenced blocks (never ASCII art); only folder/file trees use indented text
  blocks.

## Producing the review

1. Read the feature thoroughly before judging it. Read its parent spec and cited ADRs, and skim
   the folder conventions (`docs/features/CLAUDE.md`) and any existing tasks under
   `docs/tasks/FEAT-NNNN/`.
2. Work through the relevant criteria above, gathering observations for each category.
3. Report findings grouped by the categories above. For each finding, note its
   severity/priority and give a concrete, actionable suggestion — including *where* content
   should move when it sits at the wrong level (up into the spec, or down into a task/ADR).
4. Close with an overall assessment of the feature's quality and its readiness to be broken into
   tasks and built: is it traced, a true slice, soundly designed, and cleanly decomposed?
