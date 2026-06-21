# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is a **Claude Code plugin marketplace** (`earelin-plugins`) — a catalog of custom
plugins and skills, authored entirely as Markdown and JSON. There is no application code,
build step, test suite, or linter. Work consists of editing manifests and `SKILL.md`
content. "Testing" a change means installing the marketplace in Claude Code and exercising
the skill (`/plugin marketplace add earelin/claude`, then `/plugin install <name>@earelin-plugins`).

## Structure

- `.claude-plugin/marketplace.json` — the marketplace catalog. Every installable plugin
  must have an entry in its `plugins` array. `metadata.pluginRoot` is `./plugins`, so each
  entry's `source` is just the folder name (e.g. `"./backend"`).
- `plugins/<name>/` — one directory per plugin. Each contains:
  - `.claude-plugin/plugin.json` — the plugin manifest (**only** this file lives under
    `.claude-plugin/`).
  - `skills/<skill-name>/SKILL.md` — model-invoked skills. Other component types are
    auto-discovered from conventional plugin-root locations if added: `commands/*.md`,
    `agents/*.md`, `hooks/hooks.json`, `.mcp.json`.

The `github-*` skills are packaged in the `github` plugin (`plugins/github/skills/`). There
are no longer any top-level, un-packaged skills.

Current plugins: `github` (the `github-*` family of PR/issue skills), `specs` (specs-review),
`architecture` (architecture-review), `backend` (backend-review). The `specs`, `architecture`,
and `backend` manifests declare `"dependencies": ["github"]`, so installing any of them also
installs `github`.

## Conventions

- **SKILL.md frontmatter** has exactly two fields: `name` (must match the skill's directory
  name) and `description`. The `description` should state both *what* the skill does and a
  *"Use when…"* trigger clause — this is what Claude matches against to decide invocation.
- **Review skills follow a shared shape**: a one-paragraph purpose statement, then the
  instruction that the criteria are "independent prompts, not sequential steps — work
  through the ones relevant" to the subject, followed by `## Review criteria` grouped into
  `###` themed subsections of checklist questions. Match this style when adding or editing
  review skills (`specs-review`, `architecture-review`, `backend-review`).
- **GitHub skills** (`plugins/github/skills/github-*`) wrap `gh` CLI workflows. They share precondition
  patterns: confirm wording before anything outward-facing (PRs, issues, comments), never
  open a PR from the default branch, and accept a PR/issue number, URL, or nothing
  (defaulting to the current branch).
- License is **GPL-3.0**; set it in both `plugin.json` and the `marketplace.json` entry.

## Adding a plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` (copy an existing manifest as a
   template — keep `$schema`, `author`, `license`, `version`, `keywords`).
2. Add components at the plugin root (e.g. `skills/<name>/SKILL.md`).
3. Register it in `marketplace.json`'s `plugins` array with `name`, `source` (folder name),
   `description`, `version`, and `license`.
4. To make a plugin depend on another, add a `"dependencies": ["<plugin>"]` array to its
   `plugin.json` (bare names resolve against this marketplace). Bump the dependent's
   `version` in both `plugin.json` and its `marketplace.json` entry.

## Source of truth

Prefer `marketplace.json` as the source of truth for the installable plugin set, and keep
`README.md` in sync when touching it.
