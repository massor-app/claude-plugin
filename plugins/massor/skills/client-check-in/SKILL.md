---
name: client-check-in
description: This skill should be used when a trainer asks "how is <client> doing", "what did <client> do this week", "how's her squat going", "who hasn't trained lately", "prep me for my session with <client>", or wants a quick read on a client's recent training in Massor. Read-only.
version: 0.2.0
---

# Client check-ins

Give the trainer a short, useful read on a client before they talk to them. Read-only: never change anything from this skill.

## Gather

1. Resolve the client with `list_clients`. For "who hasn't trained lately", run the steps below for each client and keep it to one line each.
2. Call `get_client_overview`. It has the active programs, the next day due, any in-progress workout, `completedLast7Days`, and the five most recent workouts.
3. For the main lifts in their program, call `get_exercise_history` to see the trend. Two or three lifts is plenty.
4. Only when something needs a closer look (a skipped exercise, an odd note), open that session with `get_client_workout`. Use `list_client_workouts` to look further back than the overview.

Read dates as calendar days relative to the overview's `now`. "This week" means the last 7 days unless the trainer says otherwise.

## Report

Keep it to a few lines a trainer can read on their phone:

- **Consistency:** workouts in the last 7 days against what the program calls for, and when they last trained.
- **Open workout:** if there's an in-progress session, say so. If it's stale, it was probably abandoned and may need closing.
- **Lifts:** notable changes in load or reps on the key lifts, e.g. "Goblet squat up from 40 to 50 lb over three sessions."
- **Flags:** skipped exercises, swaps, or client notes worth a conversation.
- **Next:** one or two options for the next session, framed as suggestions for the trainer.

Lead with the most important thing. Skip sections with nothing to say. Use the client's first name, and don't repeat contact details.

## Follow-ups

When the trainer wants to act on something, carry on naturally: changing the program follows the program-design skill, and logging or fixing a workout follows the client-workouts skill.
