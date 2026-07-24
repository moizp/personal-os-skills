---
name: quarterly-goal-setting
description: >
  Use this skill when the user wants to set, plan, or revise goals for an
  upcoming 3/6/9/12-month horizon, or asks to "plan next quarter," "set
  quarterly goals," or similar. Reads config/config.yaml for which
  frameworks and horizons apply.
---

# Quarterly Goal Setting

## When this fires
User asks to set, plan, or revise goals for a 3/6/9/12-month horizon —
e.g. "let's plan next quarter," "set Q3 goals," "revise my 6-month goals."

## Inputs to read first
1. `config/config.yaml` → `frameworks`, `goals.horizons`, `goals.roles`,
   `goals.artifact_name`
2. The current quarterly-goals artifact (if one exists) — read actual
   progress before proposing new goals.
3. Recent weekly review outcomes, if available — real deliverables should
   inform revisions, not just aspiration.

## Procedure

1. **Review before planning.** Summarize what actually happened against
   the previous horizon's goals — completed, partial, dropped, and why
   (in one line each, not a full retrospective essay).

2. **If `seven_habits` is enabled**: organize goals by role
   (`goals.roles` from config). Ask which roles are being neglected before
   proposing new goals, don't just take the user's first list at face
   value.

3. **Draft goals per horizon** (3/6/9/12 months per `goals.horizons`).
   Each goal should be:
   - Outcome-stated (what does "done" look like)
   - Tagged with which role/framework motivated it
   - Flagged as either a recurring-habit goal (if `atomic_habits` enabled
     and it's identity-based/recurring) or a discrete project goal

4. **For each discrete project goal**, note that it will need its own
   todo-list/Kanban board (see the todo backend doc) — don't leave it as a
   single vague line item.

5. **If `gtd` is enabled**: confirm each project goal has at least one
   concrete next action, not just an outcome statement.

6. **Update the artifact** (`goals.artifact_name`) with the revised goal
   set. Keep the previous horizon's actuals visible for reference, don't
   overwrite history.

7. **Do not silently drop unmet goals.** Ask the user explicitly whether a
   carried-over goal should continue, be redefined, or be moved to
   Someday/Maybe.

## Output
An updated goals artifact, plus a short spoken summary of what changed and
why (in plain language, not a wall of bullet points).
