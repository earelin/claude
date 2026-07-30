# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is a **Claude Code plugin marketplace** (`earelin-plugins`) — a catalog of custom
plugins, skills, and agents, authored entirely as Markdown and JSON. There is no application
code, build step, test suite, or linter. Work consists of editing manifests, `SKILL.md`
content, and agent definitions. "Testing" a change means installing the marketplace in
Claude Code and exercising the component (`/plugin marketplace add earelin/claude`, then
`/plugin install <name>@earelin-plugins`, then `/reload-plugins` or restart).

## Structure

- `.claude-plugin/marketplace.json` — the marketplace catalog. Every installable plugin must
  have an entry in its `plugins` array. Each entry's `source` is the path from the repo root:
  `"./plugins/<name>"` (there is no `metadata.pluginRoot`, so the full `./plugins/` prefix is
  required).
- `plugins/<name>/` — one directory per plugin. Each contains:
  - `.claude-plugin/plugin.json` — the plugin manifest (**only** this file lives under
    `.claude-plugin/`).
  - Components at the plugin root, auto-discovered from conventional locations:
    `skills/<skill-name>/SKILL.md` (model-invoked skills), `agents/*.md` (custom subagents),
    and if added `commands/*.md`, `hooks/hooks.json`, `.mcp.json`.

Current plugins:

- `github` — the `github-*` family of PR/issue skills wrapping `gh` CLI workflows.
- `specs` — a `specs-review` skill.
- `architecture` — an `architecture-review` skill.
- `backend` — a `code-reviewer` agent (read-only, confidence-scored reviewer) plus a
  `backend-refactoring` skill (behaviour-preserving refactorer).
- `frontend` — the same shape (`code-reviewer` agent, `frontend-refactoring` skill), adapted to
  client-side concerns, plus the `typescript-unit-test`/`react-component-test`/`frontend-acceptance-test` skills.
- `devops` — the same shape (`code-reviewer` agent, `devops-refactoring` skill), for CI/CD
  pipeline and infrastructure-as-code changes.

The `specs`, `architecture`, `backend`, `frontend`, and `devops` manifests declare
`"dependencies": ["github"]`, so installing any of them also installs `github`.

## Conventions

- **SKILL.md frontmatter** has exactly two fields: `name` (must match the skill's directory
  name) and `description`. The `description` should state both *what* the skill does and a
  *"Use when…"* trigger clause — this is what Claude matches against to decide invocation.
- **Review skills follow a shared shape** (`specs-review`, `architecture-review`): a
  one-paragraph purpose statement, then the instruction that the criteria are "independent
  prompts, not sequential steps — work through the ones relevant" to the subject, followed by
  `## Review criteria` grouped into `###` themed subsections of checklist questions. Match
  this style when adding or editing review skills.
- **Agent definitions** (`agents/*.md`) have YAML frontmatter (`name`, `description`, `tools`,
  and optionally `model`/`color`/`modelTier`/`permissionMode`) followed by a system prompt.
  `backend`, `frontend`, and `devops` share a deliberately parallel `code-reviewer` agent
  (read-only, scores each issue 0–100 and reports only findings ≥ 26). When editing one
  plugin's `code-reviewer`, keep its counterparts in the sibling plugins in sync; they differ
  only in server-side vs. client-side vs. delivery-path framing.
- **The `*-refactoring` skills** (`backend-refactoring`, `frontend-refactoring`,
  `devops-refactoring`) are the other parallel set — an iterative, behaviour-preserving
  refactorer capped at five passes, gated on a known-green safety net, reporting only as a
  terminal summary. Keep the three in sync when editing one; they differ only in the
  domain lens (API/data contract vs. rendered output/component API vs. plan/pipeline graph).
- **GitHub skills** (`plugins/github/skills/github-*`) wrap `gh` CLI workflows. They share
  precondition patterns: confirm wording before anything outward-facing (PRs, issues,
  comments), never open a PR from the default branch, and accept a PR/issue number, URL, or
  nothing (defaulting to the current branch).
- License is **GPL-3.0**; set it in both `plugin.json` and the `marketplace.json` entry.

## Adding a plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` (copy an existing manifest as a
   template — keep `$schema`, `author`, `license`, `version`, `keywords`).
2. Add components at the plugin root (e.g. `skills/<name>/SKILL.md` or `agents/<name>.md`).
3. Register it in `marketplace.json`'s `plugins` array with `name`, `source`
   (`"./plugins/<name>"`), `description`, `version`, and `license`.
4. To make a plugin depend on another, add a `"dependencies": ["<plugin>"]` array to its
   `plugin.json` (bare names resolve against this marketplace). Bump the dependent's
   `version` in both `plugin.json` and its `marketplace.json` entry.

## Source of truth

Prefer `marketplace.json` as the source of truth for the installable plugin set, and keep
`README.md` (and each plugin's `README.md`) in sync when touching it.
