---
name: create-adr
description: Author a new Architecture Decision Record (ADR, NNNN) capturing one architecturally significant decision in Nygard format. Use when the user wants to write, draft, or propose an ADR, or when a feature/task needs a decision (new module, boundary, datastore, public-contract change, or cross-cutting pattern) that no ADR yet covers.
---

# Create an ADR

Author a new Architecture Decision Record under `docs/architecture/`. One ADR records **one**
architecturally significant decision in **Nygard format**. ADRs govern the `SPEC → feature →
task` flow: features and tasks reference the ADRs that constrain them in their `adrs:` frontmatter.

## Before you write

1. Read `docs/architecture/CLAUDE.md` if present — the repo's own conventions win over this skill.
2. **Confirm the decision is architecturally significant** — a new module or bounded context, a
   new boundary or dependency, a datastore or public-contract change, or a cross-cutting pattern.
   If it is a local implementation choice, it does not need an ADR.
3. Determine the next number: scan `docs/architecture/` for the highest `NNNN` and use the next
   sequential value, zero-padded to four digits.
4. Identify the motivating spec (`SPEC-NNNN`), if any. Note whether this decision **supersedes**
   an existing accepted ADR.

## Rules

- **One decision per ADR.** If several decisions are entangled, split them.
- **Immutable once `accepted`.** Never rewrite an accepted decision. To revise it, create a
  **new** ADR and set `supersedes` on the new record and `superseded_by` on the old one (the old
  one's `status` becomes `superseded`) — updating those two metadata fields is the only edit an
  accepted ADR may receive.
- **Number sequentially** (`0001-…`, `0002-…`); the filename has no `ADR-` prefix.
- **State the decision plainly** — an ADR decides, it does not defer.
- **Record honest consequences** — both the positive outcomes and the costs/tradeoffs accepted.
- **Diagrams are Mermaid** fenced blocks; only folder/file trees use indented text blocks.

## Format

- Filename: `docs/architecture/NNNN-kebab-title.md`.
- Frontmatter:
  ```yaml
  ---
  status: proposed         # proposed | accepted | superseded | deprecated
  date: YYYY-MM-DD         # the current date
  spec: SPEC-NNNN          # what motivated it (or null)
  supersedes: null         # NNNN of the ADR this replaces, if any
  superseded_by: null
  ---
  ```
- Body (Nygard):
  ```markdown
  # NNNN. Short decision title

  ## Status
  Proposed        # mirrors the frontmatter status

  ## Context
  The forces at play and the problem being decided.

  ## Decision
  The choice made, stated plainly.

  ## Consequences

  ### Pros
  - Positive outcomes.

  ### Cons
  - Costs and tradeoffs accepted.
  ```

## Steps

1. Confirm the decision warrants an ADR; if not, say so rather than creating one.
2. Draft the ADR following the format above, using the current date and keeping it to one
   decision.
3. Write it to `docs/architecture/NNNN-kebab-title.md`.
4. If it supersedes an existing ADR, set `supersedes:` on the new one and update the old record's
   `superseded_by:` and `status: superseded` — do not touch anything else in the old ADR.
5. Summarise for the user: the number assigned, the decision, the spec it traces to, and which
   features/tasks should reference it in their `adrs:` frontmatter.
