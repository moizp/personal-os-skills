# Personal OS — Skills for Goal Planning & Task Execution

A generic, reusable system for turning long-term goals into weekly and daily
execution, built on Claude Projects, Skills, and Cowork.

It combines six frameworks:
- **Seven Habits** (Stephen Covey) — roles, big rocks, proactive prioritisation
- **Atomic Habits** (James Clear) — identity-based habits, habit stacking
- **Culture Code** (Daniel Coyle) — psychological safety, belonging cues (most
  relevant if you're applying this to a team, not just yourself)
- **Getting Things Done** (David Allen) — capture, clarify, organise, reflect, engage
- **Lean** (Lean Startup / Lean Thinking) — timeboxing, testing the
  riskiest assumption before full commitment, small batch sizes
- **Ikigai** — long-term direction from the overlap of what you love, what
  you're good at, what the world needs, and what you can be paid for; feeds
  `long-term-goal-setting`, which grounds yearly goals (needs `seven_habits`
  enabled too)

You don't need to use all six. Pick what fits in your `config/config.yaml`
(see below).

---

## Repo structure

```
personal-os-skills/
├── README.md                  ← you are here
├── frameworks/                ← generic framework summaries (reusable as-is)
│   ├── seven-habits.md
│   ├── atomic-habits.md
│   ├── culture-code.md
│   ├── gtd.md
│   ├── lean.md
│   └── ikigai.md
├── skills/                    ← the actual Claude Skills (procedures)
│   ├── long-term-goal-setting/SKILL.md
│   ├── quarterly-goal-setting/SKILL.md
│   ├── weekly-review/SKILL.md
│   ├── daily-planning/SKILL.md
│   └── lean-experiment/SKILL.md
├── config/
│   ├── config.example.yaml    ← template — copy this
│   └── config.sample.yaml     ← a filled-in real example (see docs/sample-config-notes.md)
└── docs/
    ├── todoist-setup.md       ← Todoist project/label/kanban setup (default backend)
    ├── reminders-setup.md     ← Apple Reminders list/tag/kanban setup (alternative)
    └── sample-config-notes.md ← walkthrough of the sample config
```

**Design principle:** frameworks and skills stay generic. Everything
person-specific (your list names, which frameworks you use, your todo
backend) lives in `config/config.yaml`, which the skills read rather than
hardcode. This is what makes the repo forkable.

---

## Get Started

### 1. Clone this repo
```bash
git clone <your-fork-url> personal-os-skills
cd personal-os-skills
```

### 2. Create your config
```bash
cp config/config.example.yaml config/config.yaml
```
Edit `config/config.yaml`:
- Which frameworks you're using (comment out ones you don't want)
- Your todo backend of choice and its project/list names (Todoist by
  default — see step 3)
- Your goals artefact names — `goals.artefact_name` (the yearly/quarterly
  one, created fresh each calendar year) and, if using `ikigai`,
  `goals.long_term_artefact_name` (not year-scoped — see
  `long-term-goal-setting`)
- Your review cadence (day/time for weekly review, etc.)

Look at `config/config.sample.yaml` for a real filled-in example.

### 3. Set up your todo backend
Pick one and follow its doc:
- **Todoist (recommended)** → `docs/todoist-setup.md` (projects, sections,
  labels). Connect via Settings → Connectors in claude.ai/Desktop — no
  local server, works from any device. See "Lessons learned" below for
  why this is the default over Reminders.
- **Apple Reminders** → `docs/reminders-setup.md` (lists, tags, Kanban sections) +
  [`reminders-mcp-server`](https://github.com/amoizp/reminders-mcp-server)
  for Claude to read/write directly (macOS only, local execution) — works,
  but read the "Lessons learned" section before choosing this over Todoist.
- **Custom Yjs/CRDT app** → see the companion repo (link it here once built)
- **Other third-party app (Asana, etc.)** → connect via Claude's connector
  directory if one exists; no extra setup needed in this repo

### 4. Create your Claude Project
1. Create a new Project in Claude (e.g. "Personal OS")
2. Upload the framework docs from `frameworks/` for the ones you're using
3. Add your own "how I apply these" notes as a separate doc (keep this
   separate from the generic framework summaries — see
   `docs/sample-config-notes.md` for why)
4. Paste your project instructions (a starter template is in
   `docs/project-instructions.md`)

### 5. Install the skills
Point Claude's Skills settings (or your Cowork skill directory) at the
`skills/` folder in this repo — either by cloning directly into the expected
skills path, or symlinking it in. Skills read `config/config.yaml` at
runtime, so one install works for all of them.

### 6. (Optional) Set up Cowork scheduled tasks
Once the above works manually in chat a few times, promote the recurring
ones (daily planning, weekly review) to scheduled Cowork tasks. Don't do
this on day one — run it by hand for a couple of weeks first so you know
what the check-in should actually say.

### 7. Adding a new device
- Reminders/Shortcuts: sync automatically via iCloud, nothing to do
- Claude Project + Cowork: account-level, sign in on the new device
- This repo: `git pull` on the new machine, re-point Skills settings at it

---

## Lessons learned: why Todoist over Apple Reminders

This system originally ran on Apple Reminders, via a companion local MCP
server (`reminders-mcp-server`) that shelled out to AppleScript/JXA. It's
still a supported option (`docs/reminders-setup.md`), but it's no longer
the default — here's why, in case you're weighing the same choice.

**What worked with Reminders:**
- Full read/write control via JXA — listing, creating, completing tasks,
  setting due dates, all scriptable.
- Two real bugs got found and fixed along the way: `get_due_todos`
  originally excluded anything overdue (its date filter had a lower bound
  that should never have been there), and date-only due dates were
  landing at an arbitrary time instead of showing as all-day — both fixed
  by understanding exactly how AppleScript/JXA represents dates.
- These fixes proved the *logic* could be made correct.

**What didn't work — and turned out to be unfixable at the script level:**
- Reminders' AppleScript/JXA bridge has a large, roughly fixed overhead
  per list touched — empirically 2–7 seconds, regardless of how many
  reminders that list actually contains. A single list in isolation was
  fast; scanning multiple lists (needed for any "what's due today across
  everything" query) was not.
- Three different fix attempts were tried: sequential scanning (slow but
  predictable, ~1-7 minutes across a dozen lists), unlimited concurrent
  fan-out (catastrophically worse — up to 7 minutes for the same query,
  apparently due to Reminders' Apple Events handling degrading badly
  under concurrent load rather than serving requests in parallel), and
  concurrency-capped batching (still unreliable — a configuration that
  measured 4 seconds in isolation took 5+ minutes once integrated into
  real usage). None produced consistent, predictable performance — the
  bottleneck appears to be inside Reminders.app's own scripting bridge,
  not something a client-side script can reliably engineer around.
- Kanban sections (the columns behind quarterly-goal-setting's project
  boards) are not exposed to AppleScript/JXA at all — a permanent
  limitation, not a performance problem. `update_due_date` existed
  specifically as a workaround for this.
- No native tag/label support in the scripting bridge — the `#goal-`
  prefix convention was a naming-only workaround, not a real feature.

**Why Todoist, specifically:**
- An official Claude connector already existed — no server to build or
  keep running locally, no macOS-only constraint, works from any device.
- Verified live: instant responses (not the multi-minute waits above),
  correct overdue-inclusion by default (`find-tasks-by-date` does this
  natively — the exact behaviour that took real engineering effort to
  retrofit onto Reminders), and genuine date-only due dates with no
  workaround needed.
- Real, scriptable Kanban sections (`add-sections`/`find-sections`) —
  the permanent Reminders limitation, solved rather than worked around.
- Native labels and priority levels, replacing the `#goal-` naming hack
  entirely.
- Bonus tools that didn't exist before at all: project health/productivity
  analytics that weekly-review and quarterly-goal-setting can now draw on.

The honest summary: Reminders' local, no-cloud-dependency model is
appealing, and everything it does, it does *correctly* once you
understand its scripting quirks — the problem was purely that "correct"
and "fast enough to use" turned out to be different bars, and only one
of them was reachable from outside the app.

---

## For a new user forking this repo

1. Fork it.
2. Don't edit `skills/` or `frameworks/` unless you're improving the generic
   logic — those are meant to be shared.
3. Do edit `config/config.yaml` — that's yours.
4. Add your own "my application of these frameworks" doc to your Claude
   Project — keep it out of this repo, or put it in a `config/my-notes.md`
   that's gitignored if you want it version-controlled privately.
5. Open a PR back if you improve a skill in a way that's still generic —
   keeps the shared core good for everyone.
