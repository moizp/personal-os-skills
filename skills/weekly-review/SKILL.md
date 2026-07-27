---
name: weekly-review
description: >
  Use this skill when the user asks for a "weekly review," "weekly
  planning," or it's the scheduled weekly review time from
  config.yaml (review_cadence.weekly_review_day/time). Runs a GTD-style
  Reflect step and stages next week's priorities.
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
   `docs/reminders-setup.md`). `includeCompleted: true` here specifically,
   so completions can be reviewed against what was planned.
4. **`get_due_todos(horizon: this_month)`** — candidates to stage into
   next week.
5. The quarterly goals artifact, for alignment checking.

## Procedure

1. **Inbox triage** (if `gtd` enabled). Read the real Inbox list. For
   every item: move it to a project list, give it a due date (this makes
   it show up in future `get_due_todos` horizon queries — no list move
   needed), move it to Someday/Maybe, or flag it for deletion — nothing should
   survive this step still sitting in Inbox untouched. If it takes under
   two minutes, note it could just be done now rather than filed.

2. **Review completions.** Compare `get_due_todos(horizon: this_week,
   includeCompleted: true)` against what was actually completed. No
   judgment tone — just facts, this is data for planning, not a
   performance review.

3. **Someday/Maybe MUST be touched this step.** Per GTD, this list rots if
   skipped. Surface it explicitly: "here's what's in Someday/Maybe — anything
   ready to activate, or still parked?" Don't skip this even if the list
   looks unchanged from last week.

4. **Check alignment with quarterly goals.** Flag anything due this
   week/month that doesn't trace back to a current quarterly goal — ask
   if it should be added intentionally or dropped.

5. **If `seven_habits` enabled**:
   - Check role balance — were any roles completely absent from this
     week's actual activity?
   - **Quadrant III check.** Looking at what actually got done this week
     (from step 2) and what's staged (from step 9), flag anything that's
     urgent-but-not-important — someone else's priority landing on your
     plate, not tied to a role or quarterly goal — rather than folding it
     into generic "busyness." Don't just count these; name one or two
     specific examples and ask whether each is worth declining or
     delegating next time, not just noting the pattern in the abstract.
   - If most of the week's actual activity was Quadrant I (crises) or
     Quadrant III rather than Quadrant II (the staged big rocks), say so
     directly — that's the sign this system exists to catch, not a minor
     footnote.

6. **If `atomic_habits` enabled**: check recurring-habit todos for streak
   health. If a habit was missed multiple times, prompt: which of the
   four laws (obvious/attractive/easy/satisfying) might be broken, rather
   than just re-scheduling it identically.

7. **If `culture_code` enabled** (team context): include a lightweight
   "what got in the way" prompt, not just individual task status.

8. **If `lean` enabled**: for any timeboxed experiments (todos tagged/
   titled as experiments, e.g. "[EXP] ...") due this week, record what was
   *learned* — assumption held, broken, or inconclusive — not just
   done/not-done. A clearly-failed assumption is a useful outcome; don't
   treat it as a missed task. Flag any experiment that ran past its
   timebox without a recorded outcome.

9. **Stage next week.** From `get_due_todos(horizon: this_month)` and
   project Backlog/Next columns, pick what's realistic for next week and
   set/update due dates accordingly via `update_due_date` — this is what
   "staging" means now, since there's no This Week list to move items
   into. Keep it realistic, not aspirational.

10. **Check Kanban boards for missing due dates**: for any project list
    using Kanban sections, flag "Doing" items with no due date set at
    all — those are invisible to `get_due_todos`. Don't force the due
    date to match the column name; the column is workflow status, the
    due date is the actual deadline, and they're independent (see
    `docs/reminders-setup.md`).

## Output
A short summary: what got done, what's flagged in Someday/Maybe, what's
staged for next week (with due dates), any Quadrant III items worth
declining or delegating, and any role/habit/goal misalignment worth the
user's attention.
