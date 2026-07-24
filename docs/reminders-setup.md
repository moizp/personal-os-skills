# Apple Reminders Setup

## Lists (time-horizon lists)
Create these as standard Reminders lists (names configurable in
`config/config.yaml` under `todo_backend.reminders.lists`):
- Today
- This Week
- This Month
- This Quarter
- Maybe

## Project lists (per active goal)
For each quarterly goal that requires multiple actions:
1. Create a new list named for the project/goal.
2. Right-click → New Section, and add sections matching
   `todo_backend.reminders.kanban_sections` in config (default:
   Backlog, This Week, Today, Done).
3. Menu bar → View → As Columns to get the Kanban board view.
4. Give each task in the board a due date matching its column
   (a "Today" card should have today's due date) — this is what makes it
   also show up in the time-horizon Smart Lists below.

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

## Keeping columns and due dates in sync
Dragging a card between Kanban columns does NOT automatically update its
due date. The weekly-review and daily-planning skills include a step to
check/fix this — worth doing by hand too if you move cards outside a
planning session.
