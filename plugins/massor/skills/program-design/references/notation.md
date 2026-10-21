# Program shape and notation

Read before calling `create_program`, `update_program`, or `update_client_program`.

## create_program arguments

```json
{
  "intake": {
    "goals": ["Build general strength"],
    "daysPerWeek": 3,
    "experience": "beginner",
    "sessionMinutes": 45,
    "availableEquipment": ["Dumbbells"],
    "limitations": [],
    "preferences": ["Short sessions"],
    "fullName": "Alex"
  },
  "program": {
    "name": "Dumbbell Full Body",
    "summary": "Repeat weekly. Add a dumbbell size when every set hits the top of the range at RPE 8 or below.",
    "mode": "reusable",
    "weightUnit": "lbs",
    "weeks": [
      {
        "weekLabel": "Training Week",
        "days": [
          {
            "dayLabel": "Day A",
            "rows": [
              { "kind": "exercise", "exerciseName": "Goblet Squat", "sets": "3", "reps": "8-12", "effort": "7-8", "rest": "90s" }
            ]
          }
        ]
      }
    ]
  },
  "newExercises": ["Landmine Press"]
}
```

- Intake: only `goals` (1–12) and `daysPerWeek` (1–7) are required. `experience` is one of `detrained`, `new`, `beginner`, `intermediate`, `advanced`. `sessionMinutes` is 10–240.
- `mode: "reusable"`: no `durationWeeks`, exactly one week.
- `mode: "progressive"`: `durationWeeks` from 4 to 8, and exactly that many weeks.
- Every week has exactly `daysPerWeek` days.
- `newExercises`: names used in rows that aren't in `search_exercises`. Each must appear in a row. At most 40.
- `isPublic`: omit unless the trainer asked to share publicly.

## Rows

Exercise row: `kind: "exercise"`, `exerciseName`, `sets`, `reps` required; `weight`, `rest`, `effort`, `notes`, `groupId` optional.

Circuit header row: `kind: "circuitHeader"`, `groupId`, `name` required; `sets`, `rest` optional.

## Circuits

A circuit is one `circuitHeader` immediately followed by at least two exercise rows with the same `groupId`. Group ids are unique within a day (e.g. `"a"`, `"b"`). Exercises outside a circuit omit `groupId`.

## Notation

All values are unitless strings. Use concrete notation, never words like "moderate" or "light".

| Field | Examples |
| --- | --- |
| `sets` | `3`, `3-4`, `3+AMRAP` |
| `reps` | `8`, `8-12`, `12,10,8`, `AMRAP`, `30s`, `30s-1m` |
| `weight` | omit when unknown; `BW`, `BW+25`, `80`, `60-70`, `50 ES`, `80%` (of the client's max), `70%,75%,80%` |
| `rest` | `90s`, `2m`, `1m30s`, `60s-90s` |
| `effort` | RPE 0–10: `8`, `7-8`, `7,8,9` |

`ES` (each side) is valid only in `weight`. For unilateral reps, keep `reps` numeric and say "each side" in `notes`. Percentages of a max are written `80%`, or one per set as `70%,75%,80%`; they can't be mixed with plain loads in one cell. Workouts resolve them from the client's max lifts.

## Limits

- 1–7 days per week, at least one exercise per day, at most 40 rows per day.
- Keep labels, notes, and summaries short.
- No fields outside this shape.

## Day updates

`update_program` and `update_client_program` take one `day`: `{ "dayLabel": "...", "rows": [...] }` using the same row shape. The day is replaced as a whole, so send every row, not just the changed one. Exercise names must already exist; add a missing one with `create_exercise` first.
