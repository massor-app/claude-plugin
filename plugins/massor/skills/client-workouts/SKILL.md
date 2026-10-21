---
name: client-workouts
description: This skill should be used when a trainer asks to "log <client>'s workout", "<client> did today's session", "record her sets", "she hit 135 for 5", "mark the workout done", "swap an exercise in his last workout", "fix the weights I logged", or "delete that workout" in Massor.
version: 0.2.0
---

# Logging client workouts

Record what a client actually did, quickly and accurately, from however the trainer describes it. Talk like a coach taking notes, not like a form: no tool names, ids, or process talk.

Workouts are logs. They never change the client's program. Program changes belong to the program-design skill.

## Logging a session

1. **Find the client and the day.** Resolve the client with `list_clients` (ask only if the name is ambiguous). Call `get_client_overview`.
   - If there's an in-progress workout and it fits what the trainer described, log into that one.
   - If that in-progress workout is `stale` (days old) and the trainer means today's session, it was most likely abandoned. Ask whether to finish it as today's workout or clear it first.
   - Otherwise "today's workout" means the program's `nextDay`. When the client has more than one active program, or the trainer's description doesn't match `nextDay`, ask which day it was.
2. **Read the prescription.** Call `get_client_program` with the program and `dayId` to get that day's exercises, sets, and reps.
3. **Collect the actuals.** Match what the trainer said to the prescribed exercises. If they gave no numbers, ask once: weights and reps, or log it as prescribed? Never invent numbers.
4. **Handle swaps.** If the client did a different exercise, find it with `search_client_exercises` (close spellings come back as `suggestions` when nothing matches; confirm before using one) and log it as a replacement for that row.
5. **Confirm in plain language.** One short summary: client, day, date, each exercise with sets × reps @ weight, swaps, notes. Wait for a yes.
6. **Write it once.** New session: `create_client_workout`. Existing session: `update_client_workout` with only the changed fields. Pass a fresh, stable `retryKey`. Set `completedAt` (and `startedAt` when finishing an old session) to today's time, in epoch milliseconds, so the log lands on the right day. Omitting `status` saves a finished workout, so include the sets; pass `status: "in_progress"` to start one instead.
7. **Report back** in a line: what was logged, plus anything notable (a rep PR, a missed set).

## Editing or deleting

- Always call `get_client_workout` first and use its `sessionRowId`s; ids change after lineup edits.
- Patch only what changed. Partial set patches keep the other logged values.
- Deleting is permanent. Do it only when the trainer has named the client and the specific workout, and say it can't be undone before calling `delete_client_workout`.

## When something fails

Read the error, fix the request, and retry with a new `retryKey` only if the intended change is different. The common cases are in the reference below. Ask the trainer only when the fix changes what gets logged.

## Additional resources

- **`references/logging-semantics.md`**: create, update, and delete behaviour, set matching, active-session rules, `retryKey`, and common errors.
