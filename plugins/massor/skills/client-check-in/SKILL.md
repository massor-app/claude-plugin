---
name: client-check-in
description: Summarize how a Massor client is doing from their recent workouts and assigned program. Use when a trainer asks how a client is progressing, what they did this week, or wants notes before a session. Read-only.
---

# Massor client check-in

Read-only. Do not call any write tool from this skill.

1. Resolve the client with `list_clients`.
2. Call `get_client_workout_context` for assigned programs and recent workouts.
3. Open the most relevant sessions with `get_client_workout` when you need set-level detail.
4. Report briefly:
   - Sessions completed vs. prescribed recently, and any in-progress workout.
   - Notable load or rep changes on key lifts.
   - Skipped exercises, swaps, or client notes worth a conversation.
   - One or two suggestions for the next session, framed as options for the trainer.

If the trainer wants to act on a suggestion, switch to the `program-design` or `client-workouts` skill.
