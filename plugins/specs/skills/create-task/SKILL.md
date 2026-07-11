---
name: create-task
description: Author a new task (TASK-NNNN) inside its feature folder docs/features/FEAT-NNNN-kebab-title/ — the smallest self-contained slice of a feature, scoped to a single domain (backend, frontend, or devops) and traceable up to a spec. Use when the user wants to write, draft, or scaffold a task, or break a feature's sequencing into task files.
---

# Create a task

Author one or more `TASK-NNNN` files inside the feature's own folder,
`docs/features/FEAT-NNNN-kebab-title/`. A task is the **smallest
traceable unit of work** — a small, self-contained change committed straight to `trunk`
(trunk-based development, no long-lived branches or PRs). Tasks live on the filesystem, not on
GitHub.

## Before you write

1. **Every task must trace up:** `feat:` → parent feature → spec. Identify the parent
   `FEAT-NNNN`. If the feature (or its spec) is missing, **STOP and propose it first** (use
   `create-feature` / `create-spec`) — do not write a task against an untraced feature.
2. Locate the feature's folder `docs/features/FEAT-NNNN-kebab-title/` (it already exists,
   holding the feature's `README.md`). Prefer the feature `README.md`'s **Sequencing** section
   as the source of the task list.
3. Determine the next number: `NNNN` restarts at `0001` within each feature folder — scan that
   folder for the highest existing task and use the next value.

## Rules

- **Small and self-contained.** One task = one small change. If it is too big to commit as a
  single coherent change, split it into multiple tasks.
- **Split by domain.** Each task belongs to exactly one of `backend`, `frontend`, or `devops`;
  record it in the `domain:` frontmatter field. When a slice of work spans more than one domain
  (e.g. an API endpoint plus its UI plus its deployment), split it into one task per domain
  rather than a single cross-cutting task, and wire the ordering through `depends_on:`
  (typically `devops` groundwork → `backend` → `frontend`).
- **Trace up in frontmatter** (`feat:`) and cite governing ADRs (`adrs:`). Acceptance criteria
  trace back to the spec (e.g. `SPEC-NNNN #4`) or feature requirements.
- **Ordering goes in `depends_on:`**, not prose — list other tasks in this feature that must
  land first.
- **Status flips as work moves:** `todo → in-progress → done`.
- **Diagrams are Mermaid** fenced blocks; only folder/file trees use indented text blocks.

## Format

- Filename: `docs/features/FEAT-NNNN-kebab-title/TASK-NNNN-kebab-title.md`.
- Frontmatter:
  ```yaml
  ---
  feat: FEAT-NNNN          # parent feature (required)
  domain: backend          # backend | frontend | devops (required)
  adrs: [NNNN]            # governing ADRs, if any
  status: todo            # todo | in-progress | done
  depends_on: []          # tasks in this feature that must land first, e.g. [TASK-0001]
  ---
  ```
- Body:
  ```markdown
  # Short goal title

  A one-line goal. Note any governing ADR and what the task is limited to.

  ## Scope
  Bullets of what the change touches.

  ## Acceptance criteria
  Testable, pass/fail statements tracing to the spec (e.g. SPEC-NNNN #4) or the
  feature's requirements.
  ```

## Steps

1. Confirm the parent feature exists; if not, stop and propose it first.
2. For each task in the feature's sequencing (or the one the user asked for), draft it following
   the format above, keeping the change small and self-contained. Where a slice of work spans
   backend, frontend, and devops, break it into one task per domain and set each task's
   `domain:` accordingly.
3. Write each to `docs/features/FEAT-NNNN-kebab-title/TASK-NNNN-kebab-title.md` with
   `status: todo`, recording
   any ordering in `depends_on:` (typically `devops` → `backend` → `frontend`).
4. Summarise for the user: the tasks created, grouped by domain (backend / frontend / devops),
   their dependency order, and which spec criteria they cover.
