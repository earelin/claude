---
name: create-feature
description: Author a new feature (FEAT-NNNN) — a buildable slice of a spec where design decisions live, broken into a sequence of tasks. Each feature is a folder under docs/features/ whose README.md holds the description. Use when the user wants to write, draft, or scaffold a feature, or turn part of a spec into buildable work.
---

# Create a feature

Author a new `FEAT-NNNN` feature folder. A feature is a **buildable slice** of a spec: it is
where design decisions live, and it enumerates the small tasks that implement it. Each feature
is a folder under `docs/features/`; its `README.md` holds the description, and its tasks live
alongside it in the same folder.

## Before you write

1. Read `docs/features/CLAUDE.md` if present — the repo's own conventions win over this skill.
2. **Every feature must trace up to a spec.** Identify the parent `SPEC-NNNN`. If no spec
   covers this capability, **STOP and propose one first** (use `create-spec`) — do not design a
   feature against an untraced capability.
3. Identify any governing ADRs. If the design needs an architecturally significant decision (a
   new module or boundary, a datastore or public-contract change, a cross-cutting pattern) and
   no ADR exists, **STOP and propose the ADR first**.
4. Determine the next number: scan `docs/features/` for the highest `FEAT-NNNN` folder and use
   the next sequential value.

## Rules

- **Design lives here** — the implementation detail a spec deliberately omits: components,
  contracts, sequencing, and edge cases.
- **Trace explicitly.** Record the parent spec in `spec:` frontmatter and governing decisions in
  `adrs:`, and link them from the body.
- **A feature is a slice, not the whole spec.** State what is out of scope (and which other
  features own it).
- **Diagrams are Mermaid** fenced blocks; only folder/file trees use indented text blocks.
- The sequencing section enumerates the tasks — each item becomes one file inside the feature's
  own folder, `docs/features/FEAT-NNNN-kebab-title/` (author them with `create-task`).

## Format

- Folder: `docs/features/FEAT-NNNN-kebab-title/`; the description is its `README.md`
  (`docs/features/FEAT-NNNN-kebab-title/README.md`). Tasks live in the same folder.
- Frontmatter:
  ```yaml
  ---
  spec: SPEC-NNNN          # parent spec (required)
  adrs: [NNNN]            # governing ADRs, if any
  status: draft           # draft | active | implemented
  ---
  ```
- Body:
  ```markdown
  # FEAT-NNNN. Title

  ## Goal
  What this slice implements, linking the parent spec and governing ADRs.

  ## Scope
  Bullets of what the feature touches. Then an **Out of scope** note naming the
  separate features that own the rest.

  ## Design
  Components, contracts, and decisions — the how. Use Mermaid for any diagram.

  ## Sequencing (tasks, one small change each)
  A numbered list; each item is one small, self-contained task, linked to its
  file in this same feature folder and citing the spec criteria it satisfies
  (e.g. SPEC-NNNN #3).

  ## Edge cases
  The tricky conditions the tasks must handle, each tracing to a spec criterion.
  ```

## Steps

1. Confirm the parent spec (and ADRs) exist; if not, stop and propose them first.
2. Draft the feature following the format above — this is where design detail belongs.
3. Write it to `docs/features/FEAT-NNNN-kebab-title/README.md` with `status: draft`.
4. Summarise for the user: the number assigned, the spec/ADRs it traces to, and the task
   breakdown from the sequencing section (offer to scaffold them with `create-task`).
