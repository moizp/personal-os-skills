---
name: quarterly-goal-setting
description: >
  Use this skill when the user wants to set, plan, or revise yearly or
  quarterly goals, or asks to "plan next quarter," "set my goals for the
  year," "set quarterly goals," or similar. Reads config/config.yaml for
  which frameworks and horizons apply. Maintains a persistent goals
  artifact that daily-planning and weekly-review both read from.
---

# Quarterly Goal Setting

## When this fires
User asks to set, plan, or revise yearly or quarterly goals — e.g. "let's
plan next quarter," "set Q3 goals," "set my goals for the year," "revise
my 6-month goals."

## Inputs to read first
1. `config/config.yaml` → `frameworks`, `goals.horizons`, `goals.roles`,
   `goals.artifact_name`
2. The current calendar year's goals artifact, named
   `<goals.artifact_name> — <year>` (e.g. "quarterly-goals — 2027") — see
   "Artifact naming" below for why it's per-year, not one cumulative
   document. If it doesn't exist yet, check whether a prior year's
   artifact does before assuming this is a first run — see "Creating the
   artifact" below.
3. Recent weekly review outcomes, if available — real deliverables should
   inform revisions, not just aspiration.

## Structure: yearly goals first, then quarterly milestones
Goals are hierarchical, not a flat list per horizon:
- **Yearly (12-month) goals** are the top-level commitment — the outcome
  that matters over the year, one per relevant role.
- **Quarterly milestones** are how each yearly goal actually gets
  reached — a concrete, checkable deliverable for a specific quarter that
  ladders up to exactly one yearly goal. A quarterly milestone with no
  parent yearly goal is a flag to raise with the user, not something to
  add silently (see step 3).
- Shorter entries in `goals.horizons` (e.g. a 3-month-only setup) still
  use this shape — the "yearly goal" is just the annual framing for a
  goal that happens to only span one quarter.

## Artifact naming: one per calendar year, not one cumulative document
The goals artifact is named `<goals.artifact_name> — <year>` and scoped
to a single calendar year — a fresh artifact starts each January, rather
than one document that grows forever. A multi-year `Actuals` history
becomes slow to re-read and rewrite every single quarterly-goal-setting
run, and the artifact's own title (`# <goals.artifact_name> — <year>`)
already implies year-scoping — this makes that explicit instead of
leaving it undefined. Prior years' artifacts stay in the Project as
read-only history; nothing is ever deleted, they just stop being the
*current* one.

## Creating the artifact
There are two distinct situations where the current year's artifact
won't exist yet — handle them differently:

- **Genuine first run** (no artifact for any year exists): create the
  current year's artifact from scratch, using the shape below.

- **Year rollover** (a prior year's artifact exists, e.g.
  "quarterly-goals — 2026", but not the current year's): this is not a
  first run. Read the prior year's artifact, and for each yearly goal
  that's still active or only partially achieved, carry it forward
  explicitly into the new artifact — don't silently drop it, and don't
  silently re-copy it either without asking. Ask the user, per
  continuing goal: still pursuing this, redefining it, or done/dropped?
  The new artifact's yearly-goals section should note where a goal came
  from, e.g. "(carried forward from quarterly-goals — 2026)".

Either way, create it now as a **persistent Project artifact** — not just
a chat reply that disappears once the conversation ends. `daily-planning`
and `weekly-review` both depend on this artifact existing and being
current. Use roughly this shape (markdown, not a rigid schema — keep it
human-editable):

```
# <goals.artifact_name> — <year>

## Yearly goals
### [Role] <outcome-stated yearly goal>
(carried forward from <prior artifact> — omit this line for a genuinely new goal)
- Q1: <milestone>
- Q2: <milestone>
- Q3: <milestone>
- Q4: <milestone>

(repeat per role / per yearly goal)

## Actuals
### Q1 <year>
- <goal/milestone>: completed | partial | dropped — one line why
(append a new dated section each revision within this year; never delete
prior actuals. This section resets with each new year's artifact — full
history lives in the prior year's artifact, not duplicated here.)
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

2. **If `seven_habits` is enabled**: organise goals by role
   (`goals.roles` from config). Ask which roles are being neglected before
   proposing new goals, don't just take the user's first list at face
   value.

3. **Confirm or draft yearly goals first**, one per relevant role. Don't
   jump straight to quarterly milestones — if the user asks to "set Q3
   goals" mid-year, still check which yearly goal that quarter serves
   before drafting it; if none of the current yearly goals fit, ask
   whether this is a new yearly goal or genuinely a one-off. On a year
   rollover (see "Creating the artifact"), this is where carried-forward
   goals get confirmed, not silently assumed.

4. **Then draft/revise quarterly milestones** for the horizon(s) actually
   being planned (per `goals.horizons`). Each milestone should be:
   - Outcome-stated (what does "done" look like this quarter)
   - Explicitly tied to its parent yearly goal
   - Flagged as either a recurring-habit goal (if `atomic_habits` enabled
     and it's identity-based/recurring) or a discrete project milestone

5. **For each discrete project milestone**, note that it will need its
   own todo-list/Kanban board (see the todo backend doc) — don't leave it
   as a single vague line item.

6. **If `gtd` is enabled**: confirm each project milestone has at least
   one concrete next action, not just an outcome statement.

7. **If `lean` is enabled**: for each project milestone, identify the
   riskiest unvalidated assumption it depends on. If there is one worth
   testing before full commitment, invoke the `lean-experiment` skill to
   design a timeboxed test rather than committing the full quarter's plan
   on an unverified premise.

8. **Update the current year's artifact** with the revised yearly goals
   and quarterly milestones. Append this quarter's actuals as a new
   dated section — never overwrite or delete prior actuals within the
   year, and never edit a prior year's artifact except to fix an error.

9. **Do not silently drop unmet milestones.** Ask the user explicitly
   whether a carried-over milestone should continue next quarter, be
   redefined, or moved to Someday/Maybe. If the same milestone has now
   slipped across multiple quarters running, say so directly rather than
   carrying it forward again on autopilot — that's a sign the milestone
   itself is wrong-sized or the yearly goal needs rethinking, not just a
   scheduling issue.

## Output
An updated (or newly created) goals artifact, plus a short spoken summary
of what changed and why (in plain language, not a wall of bullet points).
