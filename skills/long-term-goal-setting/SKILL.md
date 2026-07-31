---
name: long-term-goal-setting
description: >
  Use this skill when the user wants to work out long-term direction or
  purpose — "help me find my ikigai," "what should my long-term goals
  be," "long-term goal setting," or when quarterly-goal-setting finds no
  long-term-goals artefact yet and the user wants to create one first.
  Requires both 'ikigai' and 'seven_habits' enabled in config.yaml.
  Maintains a persistent long-term-goals artefact that
  quarterly-goal-setting reads from when drafting yearly goals.
---

# Long-Term Goal Setting

## When this fires
- User explicitly asks to explore long-term direction or purpose — "help
  me find my ikigai," "what should my long-term goals be," "long-term
  goal setting."
- `quarterly-goal-setting` finds no long-term-goals artefact yet, asks
  whether to run this first, and the user agrees.
- Infrequent by design — unlike daily/weekly/quarterly, this has no
  regular cadence. Expect it to run rarely (perhaps yearly, perhaps less)
  and mostly get *read*, not re-run, by `quarterly-goal-setting`.

## Requires
Both `ikigai` and `seven_habits` in `config/config.yaml` → `frameworks`.
If either is missing, tell the user this skill is designed around both
and ask whether to proceed anyway with a reduced version (roles only, or
ikigai only) or enable the missing framework(s) first.

## Inputs to read first
1. `config/config.yaml` → `frameworks`, `goals.roles`,
   `goals.long_term_artefact_name`
2. The current long-term-goals artefact, if one exists — this is a
   revision, not a from-scratch exercise. Read what's there and what's
   changed since, don't ignore prior work.

## Procedure

1. **Work through Ikigai per relevant role** (`goals.roles`). For each
   role, ask the four questions in turn — what do you love doing in this
   role, what are you actually good at (not just what you enjoy), what
   does this role need from you (family/employer/community/etc.), and
   what of this could realistically be compensated, directly or
   indirectly. Don't skip a circle because it feels obvious — shallow
   answers here produce shallow long-term goals later.

2. **Find the overlaps, and name the gaps.** A role with strong love and
   skill but no world-needs/paid-for grounding is a hobby, not a
   long-term commitment — fine if the user is explicit that's the intent,
   not fine if treated as equivalent to a fully-grounded match. Don't
   force every role into a tidy four-way overlap if it genuinely isn't
   there yet; a named gap is more useful than a manufactured one.

3. **Draft one or two durable long-term goals per role**, each explicitly
   tied to where it sits in that role's ikigai — not just restated as an
   ambition disconnected from the circles that produced it. These are
   multi-year in scope: bigger and slower than a yearly goal, not just
   "the yearly goal, but vaguer."

4. **Do not manufacture a long-term goal for every role just for
   symmetry.** If a role genuinely has no clear long-term direction yet,
   say so and leave it open — `quarterly-goal-setting` can still set
   yearly goals for that role without a long-term parent, it'll just flag
   them as not-yet-grounded (see that skill's alignment check).

5. **Update the long-term-goals artefact**
   (`goals.long_term_artefact_name`) as a **persistent Project
   artefact** — not year-scoped, since this layer doesn't reset annually
   the way the quarterly-goals artefact does. Append a dated revision
   entry summarising what changed and why. Never delete a prior long-term
   goal outright — mark it achieved, retired, or superseded instead, so
   the history of *why* direction changed stays visible. Use roughly this
   shape (markdown, not a rigid schema — keep it human-editable):

```
# <goals.long_term_artefact_name>

## Ikigai — [Role]
- Love: <what you love doing in this role>
- Good at: <what you're actually good at>
- World needs: <what this role needs from you>
- Paid for: <what's realistically compensated>
- Overlap / gap: <where the four genuinely intersect, and what's missing>

(repeat per role)

## Long-term goals
### [Role] <durable, multi-year outcome>
- Grounded in: <which circles this sits in — flag if only one or two>
- Status: active | achieved | retired | superseded by <goal>

(repeat per role / per long-term goal)

## Revision history
### <date>
- What changed and why, one or two lines
```

## Output
An updated (or newly created) long-term-goals artefact, plus a short
spoken summary of the ikigai findings and the resulting long-term goals —
plain language, not a wall of bullet points.
