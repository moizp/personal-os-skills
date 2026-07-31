---
name: daily-planning
description: >
  Use this skill when the user asks "what should I do today," "plan my
  day," or at the scheduled daily_planning_time from config.yaml. Pulls
  from this week's due items into a small, realistic Today plan (GTD
  Engage step). Also reviews yesterday's/overdue activity: celebrates
  completions tied to real goals and helps reschedule what didn't happen.
---

# Daily Planning

## When this fires
User says "plan my day" / "what's today" / "daily planning," or a Cowork
scheduled task fires at `review_cadence.daily_planning_time`.

## Inputs to read first
1. `config/config.yaml` → `todo_backend`, `review_cadence`
2. **Query `get_due_todos` with `horizon: today` and `includeCompleted:
   true`** — this is "Today," not a named list (Today is a Smart List,
   not scriptable; see `docs/reminders-setup.md`). A horizon has no lower
   bound, so this also returns everything overdue from before, whether
   completed or not — that's what makes both the carryover check (step 2)
   and the completion check (step 3) possible from one query.
3. **Query `get_due_todos` with `horizon: this_week`** — candidates that
   could be pulled into today.
4. Current Inbox contents.
5. If `seven_habits` is enabled: the current calendar year's goals
   artefact (`<goals.artefact_name> — <year>`; see quarterly-goal-setting)
   — specifically this quarter's milestones — so today's big rock (step 6
   below) and the completion check (step 3) can name a real milestone
   instead of a generic description.

## Procedure

1. **Never invent priorities from scratch.** Today's plan is built from
   the incomplete items in step 2's query, plus candidates pulled forward
   from `get_due_todos(horizon: this_week)` if today looks light. If this
   week's query is empty too, say so rather than guessing at what matters.

2. **Reschedule what's incomplete.** From step 2's results, anything not
   completed that's overdue or was already due today: ask whether it's
   still relevant, should stay due today, or should get a new due date
   (pushed out to a specific day, or cleared back toward Someday/Maybe).
   Don't just silently carry it forward with the same stale date — that's
   how a due date stops meaning anything. Actually call `update_due_date`
   once a decision is made (see step 8), don't just note the decision in
   chat.

3. **Acknowledge what's already done.** From step 2's results, anything
   completed (finished since the last check-in, including anything that
   was overdue and got done): give a short, specific acknowledgment — name
   the actual task, not a generic "great job." If it traces back to a
   current-quarter milestone from the goals artefact (input 5), say which
   one and why it matters, rather than treating all completions the same.
   Skip this step quietly if there's nothing worth calling out — don't
   manufacture praise for routine items just to fill the step.

4. **Quick Inbox skim** (if `gtd` enabled). Not a full triage — that's
   weekly review's job. Just check whether anything in Inbox is urgent
   enough to need a due date of today; leave the rest for the weekly pass.

5. **Keep Today small.** This is the most common failure mode across
   real-world GTD/Reminders setups — Today becomes a dumping ground and
   loses meaning. Cap it to what's realistically doable; if there's more
   candidate work than fits, leave the due date as later-this-week rather
   than pulling everything to today.

6. **If `seven_habits` enabled**: make sure at least one "big rock"
   (important-not-urgent, Quadrant II) is on the list, tied to a specific
   current-quarter milestone from the goals artefact — not just described
   generically as "goal-related." If nothing in today's or this week's
   items traces back to an active milestone, say so rather than picking
   an arbitrary task to label as the big rock.

7. **If `atomic_habits` enabled**: include the day's recurring habit-todos
   automatically, don't make the user re-add them.

8. **If `lean` enabled**: give exploratory or uncertain items an explicit
   timebox (a fixed small duration, not "however long it takes"). If an
   item is an active experiment (see `lean-experiment` skill), confirm its
   timebox deadline and surface it if today is the deadline.

9. **Update the todo backend**: for anything being pulled into today, or
   rescheduled per step 2, set its due date via `update_due_date` (or
   `dueDate` on `add_todo` for new items) — this is what makes it show up
   correctly in tomorrow's query, since there's no list to move it into.

## Output
A short, concrete Today list (not a re-explanation of the whole week),
plus — only when there's something real to say — a brief note on what got
rescheduled and what got done. Quick to read at a glance, not padded.
