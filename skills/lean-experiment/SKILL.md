---
name: lean-experiment
description: >
  Use this skill when the user wants to test a risky assumption behind a
  goal before committing further — e.g. "let's test whether this will
  actually work," "design an experiment for X," "what's the riskiest part
  of this plan," or during quarterly-goal-setting when a goal rests on an
  unvalidated assumption. Requires 'lean' enabled in config.yaml.
---

# Lean Experiment / Assumption Testing

## When this fires
- User explicitly asks to test an assumption, de-risk a plan, or design
  an experiment.
- Invoked from within `quarterly-goal-setting` when a goal has a
  significant unvalidated assumption (see that skill's procedure).

## Inputs to read first
1. `config/config.yaml` → confirm `lean` is in `frameworks`; if not, ask
   whether to proceed anyway or skip.
2. The goal or plan the assumption belongs to (from the quarterly goals
   artifact, if applicable).

## Procedure

1. **Name the assumption explicitly**, in one sentence: "For this goal to
   work, it must be true that ___." If the user's request doesn't map to
   a clear falsifiable statement, help them sharpen it before proceeding —
   a vague assumption can't be tested.

2. **Check whether it's actually the riskiest one.** If the goal rests on
   multiple assumptions, ask which one, if wrong, invalidates the most
   downstream work. Test that one first, not just whichever is easiest.

3. **Design the smallest test that produces a decisive answer** — not the
   full version of the goal. Ask: what's the cheapest thing we could do
   or observe this week that would clearly confirm or refute this?

4. **Timebox it.** Every experiment gets an explicit, small time budget
   and a hard stop — propose one (e.g. "3 days," "by Friday") rather than
   leaving it open-ended.

5. **Define success/failure criteria up front**, before running the
   experiment — what result counts as "assumption held" vs. "assumption
   broken"? Write this down now; retrofitting criteria after seeing the
   result defeats the point.

6. **Log it as a todo** in the todo backend — the experiment itself is a
   timeboxed task, due at the timebox deadline, distinct from normal
   project todos (tag or title it clearly as an experiment, e.g.
   "[EXP] ...").

7. **On review** (weekly-review will surface this): record what was
   actually learned, not just done/not-done. A clearly-failed assumption
   is a valid, useful outcome — treat it as information for revising the
   goal, not as a missed task. If the assumption *held*, say so as a real
   win, specifically — a validated assumption is exactly what de-risked
   the goal, not just "task complete." If an experiment has run past its
   timebox more than once without a recorded outcome, flag it directly as
   stalled rather than quietly extending the deadline again — that's
   usually a sign the test itself was too big or too vague, not that it
   needs more time.

## Output
A one-paragraph experiment spec: the assumption, the smallest test, the
timebox, and the success/failure criteria — plus the corresponding todo
created in the backend.
