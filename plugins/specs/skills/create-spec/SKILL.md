---
name: create-spec
description: Author a new specification (SPEC-NNNN) that describes a capability at the "what" level with testable acceptance criteria. Use when the user wants to write, draft, or scaffold a new spec, or capture a capability before designing a feature.
---

# Create a spec

Author a new `SPEC-NNNN` document. A spec describes **what** a capability does, independent of
how it is built — it is the stable root of the `SPEC → feature → task` flow, and one spec can
spawn many features.

## Before you write

1. Read `docs/specs/CLAUDE.md` if present — the repo's own conventions win over this skill.
2. Determine the next number: scan `docs/specs/` for the highest `SPEC-NNNN` and use the next
   sequential value, zero-padded to four digits.
3. Clarify the capability with the user if the scope, requirements, or acceptance criteria are
   ambiguous — a spec must be unambiguous and verifiable.

## Rules

- **What, not how.** No design or implementation detail — no class names, schemas, libraries,
  data models, endpoints, or sequencing. Those belong in the feature doc.
- **Broad and stable.** Scope it to spawn many features and change rarely. If a material change
  is needed later, prefer a new spec (marking the old one `superseded`) over a rewrite.
- **Verifiable.** Every acceptance criterion must let a reader decide pass/fail without guessing.
- **Diagrams are Mermaid** fenced blocks; only folder/file trees use indented text blocks.

## Format

- Filename: `docs/specs/SPEC-NNNN-kebab-title.md`.
- Frontmatter:
  ```yaml
  ---
  status: draft        # draft | active | superseded
  ---
  ```
- Body:
  ```markdown
  # SPEC-NNNN Title

  ## Summary
  One or two paragraphs: the capability and who it serves, at the what level.

  ## Requirements
  The requirements, grouped under `###` themed subsections when there are several.
  State non-functional expectations (reliability, performance, security) where they
  matter to the capability.

  ## Acceptance criteria
  1. A numbered list of testable, pass/fail statements a downstream task can cite
     (e.g. `SPEC-NNNN #4`).
  ```

## Steps

1. Draft the spec following the format above, keeping every statement at the what level.
2. Write it to `docs/specs/SPEC-NNNN-kebab-title.md` with `status: draft`.
3. Summarise for the user: the number assigned, the capability captured, and which features it
   might spawn next (offer to draft one with `create-feature`).
