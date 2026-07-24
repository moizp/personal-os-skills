# Apple Reminders Setup

## Lists (time-horizon lists)
**Important: do not put any of these lists inside a List Group** (Personal/
Work/Uncategorised-style folders). Groups are invisible to AppleScript/JXA
— a list inside a Group can't be read or written by
`reminders-mcp-server` at all, confirmed empirically. Keep every list
below at the top level. If you want visual organization in the Reminders
sidebar, use a naming prefix (e.g. "Work — Launch X") instead of a Group.

Create these as standard Reminders lists (names configurable in
`config/config.yaml` under `todo_backend.reminders.lists`):
- Inbox — capture only; not a time-horizon, just a landing zone for
  anything new until it's triaged (see below)
- Today
- This Week
- This Month
- This Quarter
- Maybe/Later

## Inbox triage
Add anything new to Inbox the moment it comes up — no thinking required
at capture time, that's the point. Inbox items get cleared during daily
planning (a quick skim) and fully during weekly review (nothing should
survive a week untriaged). Triaging means one of:
- Move to a project list (it belongs to an active goal)
- Move to a time-horizon list (Today/This Week/This Month/This Quarter —
  it's a real near-term action)
- Move to Maybe (not now, but worth keeping)
- Delete (wasn't actually worth keeping)
- Do it immediately if it takes under two minutes, then delete it (GTD's
  two-minute rule) rather than filing it anywhere


## Project lists (per active goal)
For each quarterly goal that requires multiple actions:
1. Create a new list named for the project/goal — **also at the top
   level, not inside a Group** (same constraint as above).
2. Right-click → New Section, and add sections matching
   `todo_backend.reminders.kanban_sections` in config (default:
   Backlog, Next, Doing, Done, Archived — deliberately different names
   from the time-horizon lists above, to avoid confusing a Kanban column
   with a Smart List).
3. Menu bar → View → As Columns to get the Kanban board view.
4. **Columns and due dates are independent — don't force one to match
   the other.** The column tracks workflow status (what stage the task is
   in); the due date tracks the actual deadline. A "Doing" task can
   reasonably span several days with a due date days out — that's normal,
   not a sync problem.
   - **Backlog** / **Next** — no forced due date; set one only once there's
     a real deadline. A task can sit in Next for a couple of weeks with no
     due date yet, that's fine.
   - **Doing** — should have *some* due date once work starts (so it's not
     invisible to the Today/This Week Smart Lists), but that date is the
     actual expected completion, not automatically "today."
   - **Done** — completed = true.
   - **Archived** — completed, manually moved here later; no due-date rule.
5. The one thing worth checking periodically: **Backlog/Next items with no
   due date at all won't show up in any time-horizon Smart List** — that's
   expected (they're not urgent yet), but if something's been sitting
   there a long time, that's what the weekly review's Someday/Maybe-style
   check is for.

## Tags
Use tags (prefix configurable, default `#goal-`) to link a task back to
its quarterly goal even across different lists — e.g. `#goal-launch-x`.
This is a cross-cutting filter, separate from the Kanban board structure.

## Smart Lists
"Today" and "Scheduled" are built-in Smart Lists driven by due date — they
automatically surface anything due today/this week regardless of which
list/project it actually lives in. This is how a task can appear in both
its project's Kanban board and your daily rollup without being duplicated
(reminders can only belong to one list at a time — see project notes).

## Columns and due dates are separate axes, by design
Dragging a card between Kanban columns does NOT automatically update its
due date, and it shouldn't need to — see the mapping above. Weekly review
checks one thing here: that "Doing" items actually have a due date set
(so they're visible in the Today/This Week Smart Lists), not that the
date matches any particular column.