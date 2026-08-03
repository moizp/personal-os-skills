---
name: quarterly-goal-setting
description: >
  Use this skill when the user wants to set, plan, or revise yearly or
  quarterly goals, or asks to "plan next quarter," "set my goals for the
  year," "set quarterly goals," or similar. Reads config/config.yaml for
  which frameworks and horizons apply, and (if `ikigai` is enabled) reads
  the long-term-goals artefact from `long-term-goal-setting` to ground
  yearly goals. Maintains a persistent, per-year goals artefact that
  daily-planning and weekly-review both read from.
---

# Quarterly Goal Setting

## When this fires
User asks to set, plan, or revise yearly or quarterly goals — e.g. "let's
plan next quarter," "set Q3 goals," "set my goals for the year," "revise
my 6-month goals."

## Inputs to read first
1. `config/config.yaml` → `frameworks`, `goals.horizons`, `goals.roles`,
   `goals.artefact_name`
2. The current calendar year's goals artefact, named
   `<goals.artefact_name> — <year>` (e.g. "quarterly-goals — 2027") — see
   "Artefact naming" below for why it's per-year, not one cumulative
   document. If it doesn't exist yet, check whether a prior year's
   artefact does before assuming this is a first run — see "Creating the
   artefact" below.
3. Recent weekly review outcomes, if available — real deliverables should
   inform revisions, not just aspiration.
4. If `ikigai` is enabled: the long-term-goals artefact
   (`goals.long_term_artefact_name`; see `long-term-goal-setting`) — this
   is what yearly goals should trace back to. If it doesn't exist yet,
   see step 3 of the procedure below before drafting yearly goals.

## Structure: long-term goals first, then yearly goals, then quarterly milestones
Goals are hierarchical, not a flat list per horizon:
- **Long-term goals** (if `ikigai` is enabled — see `long-term-goal-setting`)
  are the top-level commitment: multi-year direction, grounded in ikigai,
  one or two per relevant role. This skill doesn't create these, only
  reads them.
- **Yearly (12-month) goals** are the top-level commitment when `ikigai`
  isn't enabled, or the concrete annual expression of a long-term goal
  when it is — one per relevant role. A yearly goal with no parent
  long-term goal (when `ikigai` is enabled) is a flag to raise, same
  treatment as an unparented milestone below — not a hard block, since
  not every role needs a long-term goal yet (see `long-term-goal-setting`).
- **Quarterly milestones** are how each yearly goal actually gets
  reached — a concrete, checkable deliverable for a specific quarter that
  ladders up to exactly one yearly goal. A quarterly milestone with no
  parent yearly goal is a flag to raise with the user, not something to
  add silently (see step 3).
- Shorter entries in `goals.horizons` (e.g. a 3-month-only setup) still
  use this shape — the "yearly goal" is just the annual framing for a
  goal that happens to only span one quarter.

## Artefact naming: one per calendar year, not one cumulative document
The goals artefact is named `<goals.artefact_name> — <year>` and scoped
to a single calendar year — a fresh artefact starts each January, rather
than one document that grows forever. A multi-year `Actuals` history
becomes slow to re-read and rewrite every single quarterly-goal-setting
run, and the artefact's own title (`# <goals.artefact_name> — <year>`)
already implies year-scoping — this makes that explicit instead of
leaving it undefined. Prior years' artefacts stay in the Project as
read-only history; nothing is ever deleted, they just stop being the
*current* one.

## Creating the artefact
There are two distinct situations where the current year's artefact
won't exist yet — handle them differently:

- **Genuine first run** (no artefact for any year exists): create the
  current year's artefact from scratch, using the shape below.

- **Year rollover** (a prior year's artefact exists, e.g.
  "quarterly-goals — 2026", but not the current year's): this is not a
  first run. Read the prior year's artefact, and for each yearly goal
  that's still active or only partially achieved, carry it forward
  explicitly into the new artefact — don't silently drop it, and don't
  silently re-copy it either without asking. Ask the user, per
  continuing goal: still pursuing this, redefining it, or done/dropped?
  The new artefact's yearly-goals section should note where a goal came
  from, e.g. "(carried forward from quarterly-goals — 2026)".

Either way, create it now as a **persistent Project artefact** — not just
a chat reply that disappears once the conversation ends. `daily-planning`
and `weekly-review` both depend on this artefact existing and being
current. Use roughly this shape (markdown, not a rigid schema — keep it
human-editable):

```
# <goals.artefact_name> — <year>

## Yearly goals
### [Role] <outcome-stated yearly goal>
(carried forward from <prior artefact> — omit this line for a genuinely new goal)
(grounded in: <long-term goal from the long-term-goals artefact> — omit if `ikigai` is disabled or this genuinely has no long-term parent yet)
(achieved <date> — add this once all four quarters' milestones are done;
don't delete the goal, mark it achieved so the record stays visible)
- Q1: <milestone>
- Q2: <milestone>
- Q3: <milestone>
- Q4: <milestone>

(repeat per role / per yearly goal)

## Actuals
### Q1 <year>
- <goal/milestone>: completed | partial | dropped — one line why
(append a new dated section each revision within this year; never delete
prior actuals. This section resets with each new year's artefact — full
history lives in the prior year's artefact, not duplicated here.)
```

## Procedure

1. **Review before planning — and give real credit.** Summarise what
   actually happened against the current quarter's milestones. For
   anything completed, don't fold it into a flat status line — name it
   specifically and say which yearly goal and role it advanced. A
   finished milestone is the biggest win this system tracks; treat it
   that way, not as one bullet among "completed, partial, dropped." For
   partial/dropped items, stay factual and one line each, no judgment
   tone — that's what step 9 handles. Skip this step on a genuine first
   run.

   **Check for yearly goals now fully achieved.** If every quarter's
   milestone under a yearly goal is now done (not just this quarter's),
   don't leave that implicit in a pile of individually-completed
   milestones — ask whether to mark the yearly goal itself achieved.
   If it is, and `ikigai` is enabled and the goal carries a `(grounded
   in: ...)` annotation, append a brief revision-history entry to the
   long-term-goals artefact noting the achievement and what it means for
   that long-term goal's progress (see `long-term-goal-setting`'s
   artefact shape) — a short note, not a full re-run of that skill.

2. **If `seven_habits` is enabled**: organise goals by role
   (`goals.roles` from config). Ask which roles are being neglected before
   proposing new goals, don't just take the user's first list at face
   value.

3. **Confirm or draft yearly goals first**, one per relevant role. Don't
   jump straight to quarterly milestones — if the user asks to "set Q3
   goals" mid-year, still check which yearly goal that quarter serves
   before drafting it; if none of the current yearly goals fit, ask
   whether this is a new yearly goal or genuinely a one-off. On a year
   rollover (see "Creating the artefact"), this is where carried-forward
   goals get confirmed, not silently assumed.

   If `ikigai` is enabled: check each yearly goal against the long-term-
   goals artefact — which long-term goal does this serve? If the artefact
   doesn't exist yet, ask whether to run `long-term-goal-setting` first
   (recommended, especially on a genuine first run) or proceed with
   yearly goals unparented for now. If a yearly goal genuinely doesn't
   trace to any long-term goal, that's fine to flag and proceed with —
   not every yearly goal needs deep grounding, but it shouldn't go
   unnoticed either.

4. **Then draft/revise quarterly milestones** for the horizon(s) actually
   being planned (per `goals.horizons`). Each milestone should be:
   - Outcome-stated (what does "done" look like this quarter)
   - Explicitly tied to its parent yearly goal
   - Flagged as either a recurring-habit goal (if `atomic_habits` enabled
     and it's identity-based/recurring) or a discrete project milestone

5. **For each discrete project milestone**, it needs its own Kanban
   board — create the Todoist project and its sections directly
   (`add-projects`, then `add-sections` matching
   `todo_backend.connector.todoist.kanban_sections`; see
   `docs/todoist-setup.md`) rather than just noting that it'll need one.
   Don't leave it as a single vague line item.

6. **If `gtd` is enabled**: confirm each project milestone has at least
   one concrete next action, not just an outcome statement.

7. **If `lean` is enabled**: for each project milestone, identify the
   riskiest unvalidated assumption it depends on. If there is one worth
   testing before full commitment, invoke the `lean-experiment` skill to
   design a timeboxed test rather than committing the full quarter's plan
   on an unverified premise.

8. **Update the current year's artefact** with the revised yearly goals
   and quarterly milestones. Append this quarter's actuals as a new
   dated section — never overwrite or delete prior actuals within the
   year, and never edit a prior year's artefact except to fix an error.
   If step 1 identified a newly-achieved yearly goal, add the
   `(achieved <date>)` annotation to it now rather than leaving it
   implicit in the Actuals log.

9. **Do not silently drop unmet milestones.** Ask the user explicitly
   whether a carried-over milestone should continue next quarter, be
   redefined, or moved to Someday/Maybe. If the same milestone has now
   slipped across multiple quarters running, say so directly rather than
   carrying it forward again on autopilot — that's a sign the milestone
   itself is wrong-sized or the yearly goal needs rethinking, not just a
   scheduling issue.

## Output
An updated (or newly created) goals artefact, plus a short spoken summary
of what changed and why (in plain language, not a wall of bullet points).
