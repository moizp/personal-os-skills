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
2. Current state of the todo backend's lists (Inbox, This Week, This
   Month, Maybe, and any active project Kanban boards) — actually query
   it, don't assume.
3. The quarterly goals artifact, for alignment checking.

## Procedure

1. **Inbox triage** (if `gtd` enabled). Read `todo_backend.reminders.lists.inbox`
   (or equivalent for other backends). For every item: move it to a
   project list, move it to a time-horizon list (This Week/This
   Month/etc.), move it to Maybe, or flag it for deletion — nothing should
   survive this step still sitting in Inbox. If it takes under two
   minutes, note that it could just be done now rather than filed.

2. **Review completions.** What got done this week vs. what was planned.
   No judgment tone — just facts, this is data for planning, not a
   performance review.

3. **Someday/Maybe MUST be touched this step.** Per GTD, this list rots if
   skipped. Surface it explicitly: "here's what's in Maybe — anything
   ready to activate, or still parked?" Don't skip this even if the list
   looks unchanged from last week.

4. **Check alignment with quarterly goals.** Flag anything in This
   Week/This Month that doesn't trace back to a current quarterly goal —
   ask if it should be added intentionally or dropped.

5. **If `seven_habits` enabled**: check role balance — were any roles
   completely absent from this week's actual activity?

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

9. **Stage next week.** Move/tag items from This Month → This Week based
   on capacity and priority. Keep This Week realistic, not aspirational.

10. **Check Kanban boards for missing due dates**: for any project list
   using Kanban sections, flag "Doing" items with no due date set at all —
   those are invisible to the Today/This Week Smart Lists. Don't force the
   due date to match the column name; the column is workflow status, the
   due date is the actual deadline, and they're independent (see
   `docs/reminders-setup.md`).

## Output
A short summary: what got done, what's flagged in Someday/Maybe, what's
staged for next week, and any role/habit/goal misalignment worth the
user's attention.
