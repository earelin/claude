# hello-world

A sample Claude Code plugin that demonstrates the two most common component types:

- **Command** — `/hello-world:greet [name]` greets someone (or the world).
- **Skill** — `hello-world:changelog`, a model-invoked skill that summarizes recent
  git commits into a changelog.

Use this plugin as a template for building your own.

## Install

```
/plugin marketplace add earelin/claude
/plugin install hello-world@earelin-plugins
```

## Usage

```
/hello-world:greet Xavier
```

Ask Claude for "a changelog of recent commits" to trigger the `changelog` skill.

## Structure

```
hello-world/
├── .claude-plugin/
│   └── plugin.json          # plugin manifest
├── commands/
│   └── greet.md             # slash command
└── skills/
    └── changelog/
        └── SKILL.md         # model-invoked skill
```
