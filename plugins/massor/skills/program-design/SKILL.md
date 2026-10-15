---
name: program-design
description: Design, publish, or edit a Massor training program with the trainer. Use when a trainer asks to write a program, build a block or template, change a day in a template, or adjust a program already assigned to a client.
---

# Massor program design

The Massor MCP server owns the programming rules. Load them before drafting anything.

1. Call `get_programming_skill` and follow it. It covers the intake contract, exercise naming, progression, and which tool edits which kind of program.
2. Gather the intake conversationally. Ask only for what is missing.
3. Call `search_exercises` before choosing movements. Use exact catalog names. If a movement is missing, call `create_exercise` and use the returned name.
4. Draft the full program in chat and explain the progression. Revise until the trainer explicitly approves it.
5. Only after approval, call `create_program`. Share the returned `studioUrl` so the trainer can open it in Massor.

Editing:

- Template the trainer owns: `list_programs` → `get_program` → `update_program`.
- Copy assigned to a client: `list_clients` → `get_client_program` → `update_client_program`.
- Editing a template never changes client copies. Say so when it matters.

Do not diagnose or treat injuries. For pain or medical questions, recommend a qualified professional.
