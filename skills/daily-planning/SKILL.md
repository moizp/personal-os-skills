---
name: daily-planning
description: >
  Use this skill when the user asks "what should I do today," "plan my
  day," or at the scheduled daily_planning_time from config.yaml. Pulls
  from This Week into a small, realistic Today list (GTD Engage step).
---

# Daily Planning

## When this fires
User says "plan my day" / "what's today" / "daily planning," or a Cowork
scheduled task fires at `review_cadence.daily_planning_time`.

## Inputs to read first
1. `config/config.yaml` → `todo_backend`, `review_cadence`
2. Current contents of the This Week list/board, the Inbox, and the
   existing Today list (don't assume it's empty — check for carryover
   first).

## Procedure

1. **Never invent priorities from scratch.** Today's list is drawn from
   This Week, which was staged during the weekly review. If nothing was
   staged, say so rather than guessing.

2. **Check carryover first.** Anything left in Today from yesterday: ask
   whether it's still relevant, should move to today's fresh list, or
   should go back to This Week/Maybe.

3. **Quick Inbox skim** (if `gtd` enabled). Not a full triage — that's
   weekly review's job. Just check whether anything in Inbox is urgent
   enough to need action today; leave the rest for the weekly pass rather
   than doing full triage here.

4. **Keep Today small.** This is the most common failure mode across
   real-world GTD/Reminders setups — Today becomes a dumping ground and
   loses meaning. Cap it to what's realistically doable; if there's more
   candidate work than fits, leave the rest in This Week rather than
   padding Today.

5. **If `seven_habits` enabled**: make sure at least one "big rock"
   (important-not-urgent, tied to a role/quarterly goal) is on the list,
   not just reactive small items.

6. **If `atomic_habits` enabled**: include the day's recurring habit-todos
   automatically, don't make the user re-add them.

7. **If `lean` enabled**: give exploratory or uncertain items an explicit
   timebox (a fixed small duration, not "however long it takes"). If an
   item is an active experiment (see `lean-experiment` skill), confirm its
   timebox deadline and surface it if today is the deadline.

8. **Update the todo backend** (add/move items per `todo_backend.type` —
   e.g. set due date = today in Reminders) so the change is reflected
   where the user actually sees it, not just described in chat.

## Output
A short, concrete Today list (not a re-explanation of the whole week) —
this should be quick to read at a glance.
