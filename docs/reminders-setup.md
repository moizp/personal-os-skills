# Apple Reminders Setup

## Real lists to create
**Important: every list below must be top-level, not inside a Reminders
"Group" (folder).** Groups are invisible to AppleScript/JXA — confirmed
empirically, and consistent with the fact that `sdef` defines no group
class at all. A list inside a Group can't be read or written by
`reminders-mcp-server`. If you want visual organization in the sidebar,
use a naming prefix (e.g. "Work — Launch X") instead of a Group.

Create these as standard Reminders lists (names configurable in
`config/config.yaml` under `todo_backend.reminders.lists`):
- **Inbox** — capture only; landing zone for anything new until triaged
- **Someday/Maybe** — GTD Someday/Maybe. Must be a real list you create
  yourself — don't reuse the name of any custom Smart List you've built
  (e.g. a "no due date" filter view). Smart Lists, built-in or custom, are
  invisible to AppleScript/JXA either way — see the note below.

Plus one list per active project/goal (see below).

## What NOT to create: Today / This Week / This Month / This Quarter
These are **not real lists** in this system, even though Reminders'
built-in "Today" and any custom Smart Lists you might build look like
lists in the sidebar. They're computed filters (by due date or other
criteria), and Reminders' scripting dictionary defines no "smart list"
class — there is no way for AppleScript/JXA to read a Smart List by name.

Instead, time horizons are **computed on demand** by the `get_due_todos`
tool: it scans every real list (Inbox, Someday/Maybe, project lists) and filters
by due date — `horizon: today | this_week | this_month | this_quarter`.
This is exactly what Reminders' own Smart Lists do internally; we're just
doing the same filtering ourselves so it's scriptable.

**Practical implication:** to make a task show up in "today," don't move
it to a list called Today — just set its due date to today, on whatever
real list it already lives in (Inbox, a project list, etc.).

## Inbox triage
Add anything new to Inbox the moment it comes up — no thinking required
at capture time, that's the point. Inbox items get cleared during daily
planning (a quick skim) and fully during weekly review (nothing should
survive a week untriaged). Triaging means one of:
- Move to a project list (it belongs to an active goal)
- Give it a due date if it's a real near-term action (this is what makes
  it show up in `get_due_todos` horizon queries — no list move needed)
- Move to Someday/Maybe (not now, but worth keeping)
- Delete (wasn't actually worth keeping)
- Do it immediately if it takes under two minutes, then delete it (GTD's
  two-minute rule) rather than filing it anywhere

## Project lists (per active goal)
For each quarterly goal that requires multiple actions:
1. Create a new list named for the project/goal — top-level, not inside a
   Group (same constraint as above).
2. Right-click → New Section, and add sections matching
   `todo_backend.reminders.kanban_sections` in config (default:
   Backlog, Next, Doing, Done, Archived).
3. Menu bar → View → As Columns to get the Kanban board view.
4. **Columns and due dates are independent — don't force one to match
   the other.** The column tracks workflow status; the due date tracks
   the actual deadline, and it's what `get_due_todos` filters on. A
   "Doing" task can reasonably span several days with a due date days out
   — that's normal, not a sync problem.
   - **Backlog** / **Next** — no forced due date; set one only once
     there's a real deadline.
   - **Doing** — should have *some* due date once work starts (so it's
     not invisible to horizon queries), but that date is the actual
     expected completion, not automatically "today."
   - **Done** — completed = true.
   - **Archived** — completed, manually moved here later; no due-date
     rule, just keeps Done from accumulating indefinitely.
5. Backlog/Next items with no due date at all simply won't show up in any
   `get_due_todos` result — expected, since they're not urgent yet. If
   something's been sitting there a long time regardless, that's what
   weekly review's staleness check is for.

## Tags
Use tags (prefix configurable, default `#goal-`) to link a task back to
its quarterly goal even across different lists — e.g. `#goal-launch-x`.
This is a cross-cutting filter, separate from both the Kanban board and
the horizon-query mechanism.
