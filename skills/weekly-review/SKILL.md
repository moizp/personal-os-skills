---
name: weekly-review
description: >
  Use this skill when the user asks for a "weekly review," "weekly
  planning," or it's the scheduled weekly review time from
  config.yaml (review_cadence.weekly_review_day/time). Runs a GTD-style
  Reflect step, celebrates real progress, reschedules what slipped, and
  stages next week's priorities.
---

# Weekly Review

## When this fires
User says "weekly review" / "plan this week" / "let's review the week," or
a Cowork scheduled task fires at `review_cadence.weekly_review_day` +
`weekly_review_time` from config.

## Inputs to read first
1. `config/config.yaml` → `todo_backend`, `review_cadence`, `frameworks`
2. Current Inbox and Someday/Maybe list contents, and any active project Kanban
   boards — actually query them, don't assume.
3. **`get_due_todos(horizon: this_week, includeCompleted: true)`** — this
   is "This Week," not a named list (Smart Lists aren't scriptable; see
   `docs/reminders-setup.md`). A horizon has no lower bound, so this
   includes anything overdue from before this week too, whether completed
   or not — that's the raw material for both the completion check (step 2)
   and the reschedule check (step 3). `includeCompleted: true` here
   specifically, so completions can be reviewed against what was planned.
4. **`get_due_todos(horizon: this_month)`** — candidates to stage into
   next week.
5. The current calendar year's goals artefact
   (`<goals.artefact_name> — <year>`; see quarterly-goal-setting) —
   this quarter's milestones and their parent yearly goals, including
   each yearly goal's `(grounded in: ...)` annotation when `ikigai` is
   enabled, for alignment checking and for step 2's encouragement to
   trace all the way to the long-term goal, not stop at the yearly goal.

## Procedure

1. **Inbox triage** (if `gtd` enabled). Read the real Inbox list. For
   every item: move it to a project list, give it a due date (this makes
   it show up in future `get_due_todos` horizon queries — no list move
   needed), move it to Someday/Maybe, or flag it for deletion — nothing should
   survive this step still sitting in Inbox untouched. If it takes under
   two minutes, note it could just be done now rather than filed.

2. **Review completions — and give real credit.** Compare step 3's
   results against what was actually planned. No judgment tone for what
   didn't happen (that's step 3's job) — but for what DID get done, be
   specific and genuine: name the actual accomplishment, and if it traces
   back to a current-quarter milestone or yearly goal (input 5), say which
   one and why it moved things forward — and if that yearly goal is
   itself grounded in a long-term goal, name that connection too. That's
   the fullest version of "why it matters," not an optional extra. This
   isn't a courtesy line — completions tied to a real goal deserve to be
   called out as such, not folded anonymously into "here's what got done
   this week." Skip generic praise for routine items; this is about real
   progress, not participation credit.

3. **Reschedule what's incomplete.** From step 3's results, anything not
   completed — including items overdue from before this week, now visible
   because horizons have no lower bound — needs an actual decision, not a
   silent carry-forward with the same stale due date:
   - Still relevant and realistic → set a specific new due date via
     `update_due_date`.
   - Not urgent → move toward Someday/Maybe.
   - Been slipping for multiple weeks running → say so explicitly rather
     than rescheduling it again on autopilot; ask whether it's genuinely
     blocked, no longer wanted, or needs to be broken into a smaller
     next action.

4. **Someday/Maybe MUST be touched this step.** Per GTD, this list rots if
   skipped. Surface it explicitly: "here's what's in Someday/Maybe — anything
   ready to activate, or still parked?" Don't skip this even if the list
   looks unchanged from last week.

5. **Check alignment with quarterly milestones.** Flag anything due this
   week/month that doesn't trace back to a current quarterly milestone
   (and, through it, a yearly goal, and — when `ikigai` is enabled — a
   long-term goal) — ask if it should be added intentionally or dropped.

6. **If `seven_habits` enabled**:
   - Check role balance — were any roles completely absent from this
     week's actual activity?
   - **Quadrant III check.** Looking at what actually got done this week
     (from step 2) and what's staged (from step 10), flag anything that's
     urgent-but-not-important — someone else's priority landing on your
     plate, not tied to a role or quarterly milestone — rather than
     folding it into generic "busyness." Don't just count these; name one or two
     specific examples and ask whether each is worth declining or
     delegating next time, not just noting the pattern in the abstract.
   - If most of the week's actual activity was Quadrant I (crises) or
     Quadrant III rather than Quadrant II (the staged big rocks), say so
     directly — that's the sign this system exists to catch, not a minor
     footnote.

7. **If `atomic_habits` enabled**: check recurring-habit todos for streak
   health. If a habit was missed multiple times, prompt: which of the
   four laws (obvious/attractive/easy/satisfying) might be broken, rather
   than just re-scheduling it identically.

8. **If `culture_code` enabled** (team context): include a lightweight
   "what got in the way" prompt, not just individual task status.

9. **If `lean` enabled**: for any timeboxed experiments (todos tagged/
   titled as experiments, e.g. "[EXP] ...") due this week, record what was
   *learned* — assumption held, broken, or inconclusive — not just
   done/not-done. A clearly-failed assumption is a useful outcome; don't
   treat it as a missed task. Flag any experiment that ran past its
   timebox without a recorded outcome.

10. **Stage next week.** From `get_due_todos(horizon: this_month)` and
    project Backlog/Next columns, pick what's realistic for next week and
    set/update due dates accordingly via `update_due_date` — this is what
    "staging" means now, since there's no This Week list to move items
    into. Keep it realistic, not aspirational. Distinct from step 3: this
    is newly committed work, not something that already slipped.

11. **Check Kanban boards for missing due dates**: for any project list
    using Kanban sections, flag "Doing" items with no due date set at
    all — those are invisible to `get_due_todos`. Don't force the due
    date to match the column name; the column is workflow status, the
    due date is the actual deadline, and they're independent (see
    `docs/reminders-setup.md`).

## Output
A short summary: real credit for what got done (tied to goals where it
applies), what got rescheduled or dropped and why, what's flagged in
Someday/Maybe, what's staged for next week (with due dates), any
Quadrant III items worth declining or delegating, and any role/habit/goal
misalignment worth the user's attention.
