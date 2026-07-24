# Why config.sample.yaml is separate from config.example.yaml

- `config.example.yaml` — the blank template. Copy this to `config.yaml`
  when setting up your own instance. Committed to the repo so forks always
  get the current schema.

- `config.sample.yaml` — a fully filled-in worked example, kept as a
  reference for what "done" looks like. Useful when you're not sure what
  goes in a field. Not meant to be copied directly — copy the `.example`
  file instead so you don't inherit sample values by accident.

## Why personal application notes don't live in this repo

The `frameworks/*.md` files are deliberately generic — they describe what
each framework says, not how any specific person applies it. Personal
interpretation ("here's how I specifically use big rocks in my week")
belongs in your Claude Project's own knowledge base or instructions, not
in this shared repo. This keeps the repo forkable: two people can use the
same `frameworks/gtd.md` while applying it completely differently.

If you want your personal notes version-controlled too (not just living in
Claude), keep them in a separate private repo or a gitignored
`config/my-notes.md` — don't mix them into the shared skills/frameworks
files, since that's what future merges/PRs would conflict on.
