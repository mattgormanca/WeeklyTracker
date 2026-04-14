# CLAUDE.md

Guidance for AI assistants (Claude Code, etc.) working in this repository.

## Project Overview

**WeeklyTracker** is a (planned) World of Warcraft addon that tracks all of
the weekly tasks a character can complete — see `README.md`.

As of the current state of this repository, **no source code exists yet**.
The repo contains only:

```
WeeklyTracker/
├── README.md    # one-line project description
└── CLAUDE.md    # this file
```

There is no `.toc` manifest, no `.lua` file, no build system, no tests.

## Sibling Addon: PreyJournalTab

WeeklyTracker is designed to coexist with the author's other addon,
**PreyJournalTab** (separate repository, same author). Interop notes from that
addon's README:

- PreyJournalTab uses the slash commands `/preyhunts` and `/pjt`.
- PreyJournalTab explicitly notes that `/prey` "belongs to WeeklyTracker" —
  so **WeeklyTracker should claim `/prey`** (and presumably `/weekly` or
  similar) as its slash command when it is implemented.
- Both addons target WoW: Midnight (Interface `120001` / `12.0.0.1`).
- SavedVariables name used by PreyJournalTab: `PreyJournalDB`. Pick a
  different, non-colliding name for WeeklyTracker (e.g. `WeeklyTrackerDB`).

When WeeklyTracker is implemented, the two addons should not share
SavedVariables, slash commands, or global frame names.

## Expected Structure (when code is added)

A standard WoW retail addon layout looks like:

```
WeeklyTracker/
├── WeeklyTracker.toc    # Interface: 120001, ## SavedVariables: WeeklyTrackerDB, etc.
├── WeeklyTracker.lua    # Main implementation
└── README.md
```

Every non-directive, non-comment line in the `.toc` is a file path loaded in
order. When adding a new `.lua` file, remember to add its filename to the
`.toc` — the client won't pick it up otherwise.

## Conventions (to follow once code lands)

Match the style used in the sibling PreyJournalTab addon unless the user
requests otherwise:

- **Single `.lua` file** per addon unless size demands otherwise.
- **4-space indentation**, aligned constant tables, 80-column section banners
  (`--- ... ---`).
- **Naming:** `PascalCase` functions, `camelCase` locals, `UPPER_SNAKE`
  file-scope constants.
- **Global prefix:** prefix every global (frame name, SavedVariable, slash
  command token) with `Weekly` or `WT` to avoid collisions with other addons.
- **Chat output:** `|cffRRGGBB...|r` colour escapes, with a consistent brand
  colour for the addon's chat prefix.
- **Persistent log pattern:** timestamped, category-tagged, capped at ~500
  entries, written to `<addon>DB.log`. Useful for debugging since WoW has no
  real logging facility.
- **Weekly reset:** Tuesday 15:00 UTC (US/EU standard) — compute the most
  recent past reset timestamp on login and compare against a stored value. See
  `GetWeeklyResetTimestamp` in PreyJournalTab for a reference implementation.

## Testing

No automated test suite — WoW addons are validated in-game via `/reload` and
manual interaction. Static validation is limited to:

- Syntactic eyeballing of the `.lua`.
- Confirming every file referenced in the `.toc` exists.
- Confirming the `Interface` version in the `.toc` matches the target client.

## Git Workflow

- Default branch: `main`.
- Current feature branch: `claude/add-claude-documentation-M1SzD`.
- Commit messages in this repo should be short and declarative (the sole
  existing commit is `Initial commit`).
- Do **not** open pull requests unless the user explicitly asks.

## What NOT to do

- Don't scaffold speculative files (empty `.lua`, stub `.toc`, config
  directories) before the user has asked for an implementation.
- Don't claim slash commands (`/prey`, `/weekly`, etc.) in documentation
  without confirming with the user.
- Don't import unrelated dependencies, package managers, or build tooling —
  WoW addons ship as plain Lua + a `.toc`.
- Don't create a pull request proactively.
