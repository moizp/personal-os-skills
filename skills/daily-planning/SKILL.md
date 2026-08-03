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
2. **`find-tasks-by-date` with `startDate: "today"`** — this is "Today."
   The tool includes overdue items by default
   (`overdueOption: "include-overdue"`), so this alone covers the
   reschedule check (step 2) — no separate query needed for carryover.
3. **`find-completed-tasks` with `getBy: "completion"`, `since`/`until`
   spanning since the last check-in (typically yesterday through now)**
   — what actually got finished, for the credit check (step 3). This is
   a separate tool from `find-tasks-by-date` because Todoist queries
   active and completed tasks differently — there's no combined
   "include completed" flag on the date-range tool.
4. **`find-tasks-by-date` with `startDate: "today"`, `daysCount: 7`** —
   candidates that could be pulled into today from later this week.
5. Current Inbox contents — `find-tasks` with `projectId: "inbox"`.
6. If `seven_habits` is enabled: the current calendar year's goals
   artefact (`<goals.artefact_name> — <year>`; see quarterly-goal-setting)
   — this quarter's milestones AND their parent yearly goals, including
   each yearly goal's `(grounded in: ...)` annotation when `ikigai` is
   enabled — so today's big rock (step 6) and the completion check (step
   3) can trace all the way to the long-term goal, not stop at the
   milestone.

## Procedure

1. **Never invent priorities from scratch.** Today's plan is built from
   the incomplete items in step 2's query, plus candidates pulled forward
   from step 4 if today looks light. If step 4 is empty too, say so
   rather than guessing at what matters.

2. **Reschedule what's incomplete.** From step 2's results, anything
   overdue or already due today: ask whether it's still relevant, should
   stay due today, or should get a new due date (pushed out to a specific
   day, or cleared back toward Someday/Maybe). Don't just silently carry
   it forward with the same stale date — that's how a due date stops
   meaning anything. Actually call `reschedule-tasks` once a decision is
   made, don't just note the decision in chat.

3. **Acknowledge what's already done.** From step 3's results, give a
   short, specific acknowledgment for each — name the actual task, not a
   generic "great job." If it traces back to a current-quarter milestone
   from the goals artefact (input 6), say which one and why it matters —
   and if that milestone's yearly goal is itself grounded in a long-term
   goal, name that too, not just the milestone. That's the difference
   between "you finished a task" and "this moved your actual long-term
   direction forward." Skip this step quietly if there's nothing worth
   calling out — don't manufacture praise for routine items just to fill
   the step.

4. **Quick Inbox skim** (if `gtd` enabled). Not a full triage — that's
   weekly review's job. Just check whether anything in Inbox is urgent
   enough to need a due date of today; leave the rest for the weekly pass.

5. **Keep Today small.** This is the most common failure mode across
   real-world GTD/task-app setups — Today becomes a dumping ground and
   loses meaning. Cap it to what's realistically doable; if there's more
   candidate work than fits, leave the due date as later-this-week rather
   than pulling everything to today.

6. **If `seven_habits` enabled**: make sure at least one "big rock"
   (important-not-urgent, Quadrant II) is on the list, tied to a specific
   current-quarter milestone from the goals artefact — not just described
   generically as "goal-related." When that milestone's yearly goal is
   grounded in a long-term goal, name that connection too, so the big
   rock reads as serving real long-term direction, not just this
   quarter's list. If nothing in today's or this week's items traces back
   to an active milestone, say so rather than picking an arbitrary task
   to label as the big rock.

7. **If `atomic_habits` enabled**: include the day's recurring habit-todos
   automatically, don't make the user re-add them.

8. **If `lean` enabled**: give exploratory or uncertain items an explicit
   timebox (a fixed small duration, not "however long it takes"). If an
   item is an active experiment (see `lean-experiment` skill), confirm its
   timebox deadline and surface it if today is the deadline.

9. **Update the todo backend**: for anything being pulled into today, or
   rescheduled per step 2, set its due date via `reschedule-tasks` (or
   `dueString` on `add-tasks` for new items) — date-only (`YYYY-MM-DD`)
   unless the user gave an explicit time, which shows as an all-day item
   with no time badge.

## Output
A short, concrete Today list (not a re-explanation of the whole week),
plus — only when there's something real to say — a brief note on what got
rescheduled and what got done. Quick to read at a glance, not padded.
