# Personal OS — Skills for Goal Planning & Task Execution

A generic, reusable system for turning long-term goals into weekly and daily
execution, built on Claude Projects, Skills, and Cowork.

It combines five frameworks:
- **Seven Habits** (Stephen Covey) — roles, big rocks, proactive prioritisation
- **Atomic Habits** (James Clear) — identity-based habits, habit stacking
- **Culture Code** (Daniel Coyle) — psychological safety, belonging cues (most
  relevant if you're applying this to a team, not just yourself)
- **Getting Things Done** (David Allen) — capture, clarify, organise, reflect, engage
- **Lean** (Lean Startup / Lean Thinking) — timeboxing, testing the
  riskiest assumption before full commitment, small batch sizes

You don't need to use all five. Pick what fits in your `config/config.yaml`
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
│   └── lean.md
├── skills/                    ← the actual Claude Skills (procedures)
│   ├── quarterly-goal-setting/SKILL.md
│   ├── weekly-review/SKILL.md
│   ├── daily-planning/SKILL.md
│   └── lean-experiment/SKILL.md
├── config/
│   ├── config.example.yaml    ← template — copy this
│   └── config.sample.yaml     ← a filled-in real example (see docs/sample-config-notes.md)
└── docs/
    ├── reminders-setup.md     ← Apple Reminders list/tag/kanban setup
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
- Your Reminders list names (or your todo backend of choice)
- Your quarterly-goal artifact location/naming convention
- Your review cadence (day/time for weekly review, etc.)

Look at `config/config.sample.yaml` for a real filled-in example.

### 3. Set up your todo backend
Pick one and follow its doc:
- **Apple Reminders** → `docs/reminders-setup.md` (lists, tags, Kanban sections) +
  [`reminders-mcp-server`](https://github.com/amoizp/reminders-mcp-server)
  for Claude to read/write directly (macOS only, local execution)
- **Custom Yjs/CRDT app** → see the companion repo (link it here once built)
- **Third-party app (Todoist/Asana/etc.)** → connect it via Claude's connector
  directory; no extra setup needed in this repo

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
runtime, so one install works for all three skills.

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
