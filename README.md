# earelin-plugins

A [Claude Code](https://code.claude.com) plugin marketplace — a catalog of custom
plugins you can add to Claude Code and install from.

## Add this marketplace

```
/plugin marketplace add earelin/claude
```

## Available plugins

| Plugin | Description |
| ------ | ----------- |
| [`architecture`](plugins/architecture) | An `architecture-review` skill, plus `create-adr` and `adr-review` skills for authoring and reviewing Architecture Decision Records. |
| [`backend`](plugins/backend) | A `code-reviewer` agent and a `refactoring` agent for reviewing and refactoring backend changes on the current branch or a pull request, plus a `java-unit-test` skill for writing Java unit tests. |
| [`devops`](plugins/devops) | A `code-reviewer` agent and a `refactoring` agent for reviewing and refactoring CI/CD pipeline and infrastructure-as-code changes on the current branch or a pull request. |
| [`frontend`](plugins/frontend) | A `code-reviewer` agent and a `refactoring` agent for reviewing and refactoring frontend changes on the current branch or a pull request. |
| [`github`](plugins/github) | Skills for creating and reading GitHub pull requests and issues with the `gh` CLI. |
| [`specs`](plugins/specs) | Skills to author and review specs, features, and tasks in a traceable SPEC → feature → task flow. |

The `specs`, `architecture`, `backend`, `frontend`, and `devops` plugins depend on `github`, so
installing any of them also installs `github`.

Install a plugin with:

```
/plugin install specs@earelin-plugins
```

Then run `/reload-plugins` (or restart Claude Code) to activate it.

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json      # the marketplace catalog
└── plugins/                  # one directory per installable plugin
    ├── architecture/
    ├── backend/
    ├── devops/
    ├── frontend/
    ├── github/
    └── specs/
```

## Adding a new plugin

1. Create a folder under `plugins/`, e.g. `plugins/my-plugin/`.
2. Add a manifest at `plugins/my-plugin/.claude-plugin/plugin.json` (see
   [`specs`](plugins/specs/.claude-plugin/plugin.json) for a template).
3. Add components in the conventional, auto-discovered locations at the plugin root:
   - `commands/*.md` — slash commands (`/my-plugin:name`)
   - `skills/<name>/SKILL.md` — model-invoked skills (`my-plugin:name`)
   - `agents/*.md` — custom subagents
   - `hooks/hooks.json` — event hooks
   - `.mcp.json` — bundled MCP servers

   Only `plugin.json` belongs inside `.claude-plugin/`; everything else lives at the
   plugin root.
4. Register the plugin in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)
   by adding an entry to the `plugins` array. The `source` is the path from the repo root,
   including the `./plugins/` prefix:

   ```json
   {
     "name": "my-plugin",
     "source": "./plugins/my-plugin",
     "description": "What it does",
     "version": "0.1.0",
     "license": "GPL-3.0"
   }
   ```

See the official docs for the full schema:
- https://code.claude.com/docs/en/plugin-marketplaces
- https://code.claude.com/docs/en/plugins-reference

## License

[GPL-3.0](LICENSE)
