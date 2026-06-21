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
| [`hello-world`](plugins/hello-world) | Sample plugin: a `/greet` command and a changelog skill. Use it as a template. |

Install a plugin with:

```
/plugin install hello-world@earelin-plugins
```

Then run `/reload-plugins` (or restart Claude Code) to activate it.

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json      # the marketplace catalog
└── plugins/
    └── hello-world/          # one directory per plugin
```

## Adding a new plugin

1. Create a folder under `plugins/`, e.g. `plugins/my-plugin/`.
2. Add a manifest at `plugins/my-plugin/.claude-plugin/plugin.json` (see
   [`hello-world`](plugins/hello-world/.claude-plugin/plugin.json) for a template).
3. Add components in the conventional, auto-discovered locations at the plugin root:
   - `commands/*.md` — slash commands (`/my-plugin:name`)
   - `skills/<name>/SKILL.md` — model-invoked skills (`my-plugin:name`)
   - `agents/*.md` — custom subagents
   - `hooks/hooks.json` — event hooks
   - `.mcp.json` — bundled MCP servers

   Only `plugin.json` belongs inside `.claude-plugin/`; everything else lives at the
   plugin root.
4. Register the plugin in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)
   by adding an entry to the `plugins` array. Because `metadata.pluginRoot` is set to
   `./plugins`, the `source` is just the folder name:

   ```json
   {
     "name": "my-plugin",
     "source": "./my-plugin",
     "description": "What it does",
     "version": "0.1.0"
   }
   ```

See the official docs for the full schema:
- https://code.claude.com/docs/en/plugin-marketplaces
- https://code.claude.com/docs/en/plugins-reference

## License

[GPL-3.0](LICENSE)
