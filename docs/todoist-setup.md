# Todoist Setup

## Connect the connector
In your Claude Project (claude.ai or Claude Desktop): Settings → Connectors
→ Todoist → Connect, and authenticate via Todoist's own login (OAuth) —
this is an account-level action only you can do, Claude can't complete it
for you. Once connected, tools like `find-tasks`, `add-tasks`,
`find-tasks-by-date`, `reschedule-tasks`, and `add-sections` become
available in that Project's chats.

## Real projects to create
- **Inbox** — built-in, no setup needed (project id `"inbox"`).
- **Someday/Maybe** (`todo_backend.connector.todoist.someday_maybe_project`)
  — GTD Someday/Maybe. Create this as a real project yourself.

Plus one project per active goal that needs multi-step tracking (see
below) — created ad hoc, not up front.

## Time horizons — computed natively, no workaround needed
Unlike Reminders (see `docs/reminders-setup.md` for why that needed a
custom `get_due_todos` reimplementation of Smart Lists), Todoist's
`find-tasks-by-date` tool computes "today"/"this week"/etc. natively,
server-side, including overdue items by default
(`overdueOption: "include-overdue"`). No equivalent to Reminders' Smart
List limitation exists here — there's nothing to work around.

## Project boards (per active goal)
For each goal that needs multi-step tracking:
1. Create a real Todoist project for it.
2. Add sections matching `todo_backend.connector.todoist.kanban_sections`
   (default: Backlog, Next, Doing, Done, Archived) via the `add-sections`
   tool — **this is scriptable**, a skill can do it directly when a new
   project is created, not just read existing sections like Reminders
   required.
3. **Sections and due dates are independent — don't force one to match
   the other.** The section tracks workflow status; the due date tracks
   the actual deadline, and `find-tasks-by-date` filters on the due date,
   not the section.
   - **Backlog** / **Next** — no forced due date; set one only once
     there's a real deadline.
   - **Doing** — should have *some* due date once work starts (so it's
     not invisible to horizon queries), but that date is the actual
     expected completion, not automatically "today."
   - **Done** — completed via `complete-tasks`.
   - **Archived** — completed, manually moved here later.
4. Backlog/Next items with no due date won't show up in any
   `find-tasks-by-date` result — expected, since they're not urgent yet.
   Weekly review's staleness check is what catches items sitting there
   too long regardless.

## Inbox triage
Add anything new to Inbox the moment it comes up. Inbox items get cleared
during daily planning (a quick skim) and fully during weekly review.
Triaging means one of:
- Move to a project (`update-tasks`, change `projectId`) — it belongs to
  an active goal
- Give it a due date via `reschedule-tasks` if it's a real near-term
  action — no project move needed, it'll show up in horizon queries
- Move to Someday/Maybe
- Delete
- Do it immediately if it takes under two minutes (GTD's two-minute rule)

## Due dates: date-only by default
`reschedule-tasks` accepts either `YYYY-MM-DD` (date-only — Todoist
renders this with no time, and it's what the free ICS calendar feed shows
as an all-day event) or `YYYY-MM-DDTHH:MM:SS` (a specific time). Use
date-only unless the user explicitly gives a time — this is native to
Todoist's data model, not a workaround like Reminders needed.

## Labels
Native Todoist labels (`add-labels`/`find-labels`) link a task back to
the goal it serves across projects — prefix configurable
(`todo_backend.connector.todoist.label_prefix`, default `goal-`), e.g.
`goal-launch-x`. No tag-prefix hack needed; this is a first-class Todoist
feature.

## Calendar visibility
Todoist's free ICS calendar feed (Settings → Integrations → Calendar
feed in the Todoist app) gives one-way, read-only visibility of your
tasks in Apple Calendar — date-only due dates show as all-day events.
True two-way sync isn't free (Todoist's own Calendar Sync feature is paid
and lists Apple Calendar as beta); the free feed is what this system
assumes.
