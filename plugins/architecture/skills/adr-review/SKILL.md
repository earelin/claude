---
name: adr-review
description: Review an Architecture Decision Record (ADR, NNNN) for significance, a clearly-stated decision, honest consequences, traceability, and correct supersession. Use when the user asks for a review of an ADR, or wants to confirm a decision record is sound before accepting it.
---

# ADR Review skill

Analyze an Architecture Decision Record and provide structured, actionable feedback on its
quality. An **ADR** (`NNNN`, under `docs/architecture/`) records **one** architecturally
significant decision in **Nygard format** — context, decision, consequences. A good ADR captures
a single significant decision, states it plainly, records honest tradeoffs, traces to what
motivated it, and — once accepted — is immutable except through a superseding ADR.

This skill reviews the **ADR itself**. Judging the spec that motivated it is the `specs-review`
skill's job; judging a feature or task that references it is `feature-review` / `task-review`.

Use the criteria below as a checklist. They are independent prompts, not sequential steps —
work through the ones relevant to the ADR under review (not every question applies to every ADR).

## Review criteria

### Significance & scope

- Is this genuinely an architecturally significant decision — a new module or bounded context, a
  boundary or dependency, a datastore or public-contract change, or a cross-cutting pattern?
- Is it **one** decision, not several entangled ones that should be separate ADRs?
- Or is it a local implementation choice that does not warrant an ADR at all?

### Context

- Are the forces at play and the problem being decided stated clearly?
- Could a future reader, without prior knowledge, understand *why* this decision was needed?
- Are the alternatives or constraints that shaped the choice visible?

### Decision

- Is the decision stated plainly and unambiguously — does the ADR actually **decide**, rather
  than defer or describe options?
- Is it specific enough for features and tasks to build against it?

### Consequences

- Are **both** the positive outcomes and the costs/tradeoffs recorded honestly (not only the
  upside)?
- Do the consequences follow from the decision, and do they surface the discipline or follow-up
  the choice demands?

### Traceability

- Does `spec:` cite the motivating spec where one exists (or `null` when genuinely unmotivated by
  a spec)?
- Is the ADR referenced by the features/tasks it governs (their `adrs:` frontmatter)? Flag
  governed work that fails to cite it.

### Immutability & supersession

- If this ADR revises an earlier decision, is it a **new** ADR with `supersedes` set — and is the
  old record updated with `superseded_by` and `status: superseded` — rather than an accepted ADR
  rewritten in place?
- Are the `status` transitions valid (`proposed → accepted`, or `→ superseded` / `→ deprecated`)?
- Does the `## Status` section match the frontmatter `status:`?

### Format & conventions

- Filename follows `NNNN-kebab-title.md` with a sequential `NNNN` (no `ADR-` prefix).
- Frontmatter present: `status:`, `date:`, `spec:`, `supersedes:`, `superseded_by:`.
- Body carries the Nygard sections (Status, Context, Decision, Consequences).
- Diagrams are Mermaid fenced blocks (never ASCII art); only folder/file trees use indented text
  blocks.

## Producing the review

1. Read the ADR thoroughly before judging it. Read the motivating spec, any ADR it supersedes or
   is superseded by, and skim the folder conventions (`docs/architecture/CLAUDE.md`).
2. Work through the relevant criteria above, gathering observations for each category.
3. Report findings grouped by the categories above. For each finding, note its
   severity/priority and give a concrete, actionable suggestion — including whether the decision
   should be split, whether it needs a superseding ADR rather than an edit, or whether it should
   not be an ADR at all.
4. Close with an overall assessment of the ADR's quality and its readiness to be accepted: is it
   significant, singular, plainly decided, honestly weighed, and correctly traced?
