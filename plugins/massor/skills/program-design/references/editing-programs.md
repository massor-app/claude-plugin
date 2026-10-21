# Editing programs already in Massor

Massor keeps three separate things. Use the matching tools; editing one never changes the others.

| Thing | What it is | Read | Change |
| --- | --- | --- | --- |
| Template | The trainer's own program in their studio | `list_programs` → `get_program` | `update_program` |
| Assigned copy | A client's own copy of a program, made when it was assigned | `get_client_overview` (program ids) → `get_client_program` | `update_client_program` |
| Workout log | One session a client did, with actual sets | `list_client_workouts` / `get_client_workout` | the client-workouts skill |

- Editing a template does not change copies already assigned to clients. When the trainer edits a template a client is on, ask whether the client's copy should change too, and update it separately if so.
- Editing a client's copy does not change the template or past logs.
- `list_programs` leaves out drafts the in-app coach made for one client; `get_program` still opens one by id.
- Passing a template id to a client tool, or the reverse, fails with a hint naming the right tool. Switch tools; don't retry the same call.

## Changing a day

1. Find the program and day. For a client, `get_client_overview` lists their active programs and day ids; `get_client_program` with a `dayId` returns just that day.
2. Read the current day and make the change on top of it. Updates replace the whole day, so keep every row the trainer didn't ask to change, exactly as you read it. Rows written in the studio can hold notation new rows may not use (for example `15 ES` in reps or `RPE 8` in effort); an unchanged row is accepted as it is, but a row you change must follow `notation.md`, so move that meaning into `notes` and mention it to the trainer.
3. Check any new exercise with `search_exercises`. If it doesn't exist, tell the trainer it'll be added to their library, then call `create_exercise` and use the returned name.
4. Show the trainer the day as it will look and get a yes.
5. Call `update_program` or `update_client_program` once for that day. Repeat per day for multi-day changes, confirming the full set of changes once up front.

## Not available through Massor's tools

- Assigning a template to a client, deleting a program, or adding or removing whole days and weeks. Point the trainer to the Massor studio for these.
- Creating a second program to "replace" an existing one. Edit the existing days instead.
