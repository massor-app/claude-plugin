---
name: client-workouts
description: Log, edit, complete, or delete a client's workout in Massor. Use when a trainer says what a client did in a session, wants to record sets and weights, swap an exercise in a logged workout, or remove a workout.
---

# Massor client workouts

The Massor MCP server owns the workout rules. Load them first.

1. Call `get_workout_logging_skill` and follow it. It covers create, patch, and delete semantics and `retryKey` handling.
2. Resolve the client with `list_clients` when the name is ambiguous.
3. Call `get_client_workout_context` to see assigned programs, day prescriptions, any in-progress session, and recent workouts.
4. For edits or deletes, call `get_client_workout` first and reuse its IDs exactly. Never invent IDs.
5. For substitutions, call `search_client_exercises`.
6. Summarize the change in plain language (client, day, exercises, sets) and wait for the trainer to confirm.
7. Then call `create_client_workout`, `update_client_workout`, or `delete_client_workout` with a stable `retryKey`.

Deletes are permanent. Require the trainer to name the client and the workout day before deleting.
