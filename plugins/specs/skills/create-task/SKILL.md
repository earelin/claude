---
name: create-task
description: Author a new task (TASK-NNNN) under docs/tasks/FEAT-NNNN/ — the smallest self-contained slice of a feature, traceable up to a spec. Use when the user wants to write, draft, or scaffold a task, or break a feature's sequencing into task files.
---

# Create a task

Author one or more `TASK-NNNN` files under `docs/tasks/FEAT-NNNN/`. A task is the **smallest
traceable unit of work** — a small, self-contained change committed straight to `trunk`
(trunk-based development, no long-lived branches or PRs). Tasks live on the filesystem, not on
GitHub.

## Before you write

1. Read `docs/tasks/CLAUDE.md` if present — the repo's own conventions win over this skill.
2. **Every task must trace up:** `feat:` → parent feature → spec. Identify the parent
   `FEAT-NNNN`. If the feature (or its spec) is missing, **STOP and propose it first** (use
   `create-feature` / `create-spec`) — do not write a task against an untraced feature.
3. Locate the feature's folder `docs/tasks/FEAT-NNNN/` (create it if this is its first task).
   Prefer the feature doc's **Sequencing** section as the source of the task list.
4. Determine the next number: `NNNN` restarts at `0001` within each `FEAT-NNNN/` folder — scan
   that folder for the highest existing task and use the next value.

## Rules

- **Small and self-contained.** One task = one small change. If it is too big to commit as a
  single coherent change, split it into multiple tasks.
- **Trace up in frontmatter** (`feat:`) and cite governing ADRs (`adrs:`). Acceptance criteria
  trace back to the spec (e.g. `SPEC-NNNN #4`) or feature requirements.
- **Ordering goes in `depends_on:`**, not prose — list other tasks in this feature that must
  land first.
- **Status flips as work moves:** `todo → in-progress → done`.
- **Diagrams are Mermaid** fenced blocks; only folder/file trees use indented text blocks.

## Format

- Filename: `docs/tasks/FEAT-NNNN/TASK-NNNN-kebab-title.md`.
- Frontmatter:
  ```yaml
  ---
  feat: FEAT-NNNN          # parent feature (required)
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
   the format above, keeping the change small and self-contained.
3. Write each to `docs/tasks/FEAT-NNNN/TASK-NNNN-kebab-title.md` with `status: todo`, recording
   any ordering in `depends_on:`.
4. Summarise for the user: the tasks created, their dependency order, and which spec criteria
   they cover.
