# Massor Claude plugin

Lets trainers drive Massor from Claude: design and publish programs, edit templates and client programs, log and edit client workouts, and get client check-ins.

The plugin bundles the remote Massor MCP server (`https://mcp.massor.app/mcp`) and three skills. The first tool call opens a browser to sign in with a Massor trainer account (OAuth). Connections can be revoked in Massor under Settings → MCP.

## Install (Claude Code)

```
/plugin marketplace add massor-app/claude-plugin
/plugin install massor@massor
```

## Layout

- `.claude-plugin/marketplace.json`: marketplace with one plugin
- `plugins/massor/.claude-plugin/plugin.json`: plugin manifest
- `plugins/massor/.mcp.json`: Massor MCP server
- `plugins/massor/skills/`: `program-design`, `client-workouts`, `client-check-in`

The detailed programming and workout rules live server-side in `shared/programming/SKILL.md` and `shared/workout-logging/SKILL.md` and are served through the MCP (`get_programming_skill`, `get_workout_logging_skill`). The plugin skills stay thin so they never drift from the server.

## Local test

```
claude --plugin-dir plugins/massor
```

Source of truth lives in the private Massor repo under `claude-plugin/`; this repo is a published copy.
