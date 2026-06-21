---
name: changelog
description: Summarize recent git commits into a concise, human-readable changelog. Use when the user asks for a changelog, release notes, or a summary of recent changes.
---

# Changelog skill

Produce a concise changelog from the repository's recent git history.

## Steps

1. Run `git log --oneline -n 20` (or a range the user specifies) to gather recent commits.
2. Group the commits into categories: **Features**, **Fixes**, **Docs**, **Refactor**, **Other**.
3. Rewrite each entry as a short, user-facing bullet — drop noise like "wip" or "fixup" commits.
4. Output the result as Markdown, newest changes first.

Keep it tight: one bullet per meaningful change, no internal jargon.
