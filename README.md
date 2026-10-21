# Massor Claude plugin

Lets trainers drive Massor from Claude: design programs and save them to their studio, edit templates and client programs, log and edit client workouts, and get client check-ins.

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
- `plugins/massor/skills/<skill>/SKILL.md`: when the skill applies and how to run the conversation
- `plugins/massor/skills/<skill>/references/`: detail Claude loads only when needed (notation, programming principles, logging semantics)

Skills:

- `program-design`: draft a program with the trainer, then set it up in Massor. Exercises the trainer names that don't exist yet are added to their library when the program is created.
- `client-workouts`: log, edit, or delete a client's workout.
- `client-check-in`: a short read on how a client is doing.

The skills here are the full guidance and are maintained in this plugin. The MCP server only sends a short set of hard rules (ids, confirming writes, templates vs client copies vs logs). The Massor repo's `shared/*/SKILL.md` files are separate server-side copies and may differ on purpose.

## Local test

```
claude --plugin-dir plugins/massor
```

Source of truth lives in the private Massor repo under `claude-plugin/`; this repo is a published copy.
