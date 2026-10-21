# Workout logging semantics

## Scope

- Only the trainer's own clients, and only workouts on programs that trainer assigned.
- Never invent client, program, day, session, or session row ids. Copy them from tool responses.
- Logs are session-local snapshots. Nothing here changes the program prescription.

## Create (`create_client_workout`)

- Snapshots the chosen assigned day into a new session. The day is a scaffold: lineup changes and set results may differ from the prescription.
- `lineup` changes: `replace` (by `exerciseName` or row `order`, with the new `libraryExerciseId`), `remove`, `add`.
- `sets`: match an exercise by `exerciseName` or by absolute row `order` from `get_client_program` / `get_client_workout`. Order counts circuit headers, but only exercise rows take sets. `setNumber` is 1-based.
- Omit `status` to log a finished workout (`completed`). Use `in_progress` only when the client is mid-session.
- Pass `startedAt` / `completedAt` (ms since epoch) when the workout wasn't today or when finishing an old session.
- If the client already has an in-progress workout for the same day, create updates that session instead (`reusedActiveSession: true`). Omit `status` and `sessionNotes` then unless they should change.
- If the client has an in-progress workout for a different day, create fails. Ask the trainer whether to finish or delete that one first.
- `existing: true` in the response means the same `retryKey` and payload already ran; treat it as success.

## Update (`update_client_workout`)

Read the session with `get_client_workout` first, then send only the operations needed:

- `set_status`, `set_session_notes`, `set_timestamps`
- `set_set` (by `sessionRowId` + `setNumber`): only the fields sent change; `clear: true` wipes the set
- `set_exercise_notes`
- `add_exercise`, `replace_exercise`, `remove_exercise`, `reorder_exercises`

Notes:

- `replace_exercise` discards that exercise's logged actuals.
- Completed workouts can be edited. Reopening one with `set_status: "in_progress"` fails (`active_session_conflict`) if another workout is already active.
- `reorder_exercises` lists every row, circuit headers included. Each circuit's header and its exercises stay together, header first.

## Delete (`delete_client_workout`)

Permanently removes the session, its rows, and its set logs. Require the trainer to name the client and the workout, and say it can't be undone.

## retryKey

- Every write takes a `retryKey` (8–128 chars). Make it stable for the intended change, e.g. `log-<clientFirstName>-<yyyy-mm-dd>-<day>`.
- Retrying the same key with the same payload is safe.
- Reusing a key with a different payload returns `retry_key_mismatch`. Use a new key when the intended change is different.

## Common errors

| Error | Meaning | Fix |
| --- | --- | --- |
| `client_not_found` | Not this trainer's client | Re-resolve with `list_clients` |
| `program_not_found`, `day_not_found` | Program or day isn't assigned by this trainer | Re-read `get_client_overview` |
| `active_session_conflict` | Another workout is in progress | Ask to finish or delete it first |
| `retry_key_mismatch` | Key reused for a different change | New `retryKey` |
| `invalid_match`, `ambiguous_exercise` | A set or lineup change doesn't point at exactly one exercise row | Match by row `order` instead of name |
| `session_row_not_found`, `session_not_found` | Stale ids | Re-read with `get_client_workout` |
| `exercise_not_found`, `exercise_forbidden` | The swap exercise isn't available to this client | Pick one from `search_client_exercises` |
| `invalid_set_number`, `invalid_operation`, `invalid_reorder` | Malformed patch | Fix the operation; reorder must list every row (circuit headers included) once, keeping each circuit's header and exercises together, header first |
