---
name: daily-planning
description: >
  Use this skill when the user asks "what should I do today," "plan my
  day," or at the scheduled daily_planning_time from config.yaml. Pulls
  from this week's due items into a small, realistic Today plan (GTD
  Engage step).
---

# Daily Planning

## When this fires
User says "plan my day" / "what's today" / "daily planning," or a Cowork
scheduled task fires at `review_cadence.daily_planning_time`.

## Inputs to read first
1. `config/config.yaml` → `todo_backend`, `review_cadence`
2. **Query `get_due_todos` with `horizon: today`** — this is "Today," not
   a named list (Today is a Smart List, not scriptable; see
   `docs/reminders-setup.md`). This also naturally shows any carryover:
   anything still due today or overdue from before.
3. **Query `get_due_todos` with `horizon: this_week`** — candidates that
   could be pulled into today.
4. Current Inbox contents.

## Procedure

1. **Never invent priorities from scratch.** Today's plan is built from
   what `get_due_todos(horizon: today)` already returns, plus candidates
   pulled forward from `get_due_todos(horizon: this_week)` if today looks
   light. If this week's query is empty too, say so rather than guessing
   at what matters.

2. **Check carryover first.** Anything `get_due_todos(horizon: today)`
   returns that's from a prior day (overdue) or was already "today"
   yesterday: ask whether it's still relevant, should stay due today, or
   should get a new due date (pushed out, or cleared back toward Someday/Maybe).

3. **Quick Inbox skim** (if `gtd` enabled). Not a full triage — that's
   weekly review's job. Just check whether anything in Inbox is urgent
   enough to need a due date of today; leave the rest for the weekly pass.

4. **Keep Today small.** This is the most common failure mode across
   real-world GTD/Reminders setups — Today becomes a dumping ground and
   loses meaning. Cap it to what's realistically doable; if there's more
   candidate work than fits, leave the due date as later-this-week rather
   than pulling everything to today.

5. **If `seven_habits` enabled**: make sure at least one "big rock"
   (important-not-urgent, tied to a role/quarterly goal) is on the list,
   not just reactive small items.

6. **If `atomic_habits` enabled**: include the day's recurring habit-todos
   automatically, don't make the user re-add them.

7. **If `lean` enabled**: give exploratory or uncertain items an explicit
   timebox (a fixed small duration, not "however long it takes"). If an
   item is an active experiment (see `lean-experiment` skill), confirm its
   timebox deadline and surface it if today is the deadline.

8. **Update the todo backend**: for anything being pulled into today, set
   its due date to today via `update_due_date` (or `dueDate` on
   `add_todo` for new items) — this is what makes it show up in tomorrow's
   `get_due_todos(horizon: today)`, since there's no list to move it into.

## Output
A short, concrete Today list (not a re-explanation of the whole week) —
this should be quick to read at a glance.
