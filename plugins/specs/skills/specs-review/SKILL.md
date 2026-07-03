---
name: specs-review
description: Review a specification for quality and conformance to the SPEC → feature → task authoring flow. Use when the user asks for a review of a specification, or wants to understand how ready a spec is to spawn features and tasks.
---

# Specifications Review skill

Analyze a specification and provide structured, actionable feedback on its quality and its
fitness within a traceable authoring flow: a **spec** (`SPEC-NNNN`) describes a capability at
the **what** level; each buildable slice becomes a **feature** (`FEAT-NNNN`) where design lives;
each feature is cut into small **tasks** (`docs/tasks/FEAT-NNNN/TASK-NNNN`); and architecturally
significant decisions are captured as **ADRs**. A good spec is broad, stable, and verifiable —
it stays at the what level and pushes design, sequencing, and decisions down to features, tasks,
and ADRs.

This skill reviews the **spec document itself**. Reviewing a feature's design or a task's scope
is the job of the `feature-review` and `task-review` skills — here, only judge whether the spec
is sound and ready to spawn features.

Use the criteria below as a checklist. They are independent prompts, not sequential steps —
work through the ones relevant to the specification under review (not every question applies
to every spec).

## Review criteria

### Level of abstraction — the "what"

- Does the spec describe a capability independent of how it is built?
- Is it free of design and implementation detail — no class names, schemas, libraries, data
  models, or sequencing? Those belong in the feature doc.
- Does anything here actually belong one level down (in a `FEAT-NNNN` feature or a `TASK`) or
  in an ADR (an architecturally significant decision)? Flag it and say where it should move.

### Requirements quality

- Are the requirements complete, correct, and internally consistent?
- Is ambiguity removed — can a reader interpret each requirement only one way?
- Is the specification realistic and achievable?
- Are non-functional expectations (reliability, performance, security, operability) stated at
  the what level where they matter to the capability?

### Acceptance criteria & verifiability

- Are there explicit, **testable acceptance criteria**?
- Can a reader decide pass/fail for each criterion without guessing?
- Is each criterion phrased so a downstream task can trace back to it (e.g. `SPEC-0001 #4`)?

### Breadth & stability

- Is the spec broad and stable enough to spawn *many* features rather than describing a single
  slice of work?
- Would a material change to a requirement be better handled by a **new** spec (marking this one
  `superseded`) than by rewriting this one in place?
- Is it scoped to change rarely?

### Traceability & flow fit

- Can the capability be decomposed into buildable feature slices? Is that decomposition obvious
  enough that features can cite this spec in their `spec:` frontmatter?
- Are there implied architectural decisions that should be raised as ADRs before features build
  against them?
- Does the spec avoid pre-empting task-level sequencing or dependencies?

### Format & conventions

- Filename follows `SPEC-NNNN-kebab-title.md` with a sequential `NNNN`.
- Frontmatter present with a valid `status:` (`draft` | `active` | `superseded`).
- Diagrams are Mermaid fenced blocks (never ASCII art); only folder/file trees use indented
  text blocks.

## Producing the review

1. Read the specification thoroughly before judging it. If sibling docs are available, skim the
   folder conventions (`docs/specs/CLAUDE.md`) and any features or ADRs that reference it.
2. Work through the relevant criteria above, gathering observations for each category.
3. Report findings grouped by the categories above. For each finding, note its
   severity/priority and give a concrete, actionable suggestion — including *where* content
   should move when it sits at the wrong level (feature, task, or ADR).
4. Close with an overall assessment of the spec's quality and its readiness to spawn features:
   is it at the right level, verifiable, broad, and stable enough to proceed?
