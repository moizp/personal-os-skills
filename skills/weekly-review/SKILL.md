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
2. Current state of the todo backend's lists (This Week, This Month,
   Maybe, and any active project Kanban boards) — actually query it, don't
   assume.
3. The quarterly goals artifact, for alignment checking.

## Procedure

1. **Inbox/capture check** (if `gtd` enabled). Anything sitting
   uncategorized needs to be clarified: actionable now, a project, or
   Someday/Maybe.

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

8. **Stage next week.** Move/tag items from This Month → This Week based
   on capacity and priority. Keep This Week realistic, not aspirational.

9. **Sync Kanban boards**: for any project list using Kanban sections, make
   sure due dates match section (per `todo_backend.reminders.kanban_sections`)
   — a card in "This Week" should have a due date this week.

## Output
A short summary: what got done, what's flagged in Someday/Maybe, what's
staged for next week, and any role/habit/goal misalignment worth the
user's attention.
