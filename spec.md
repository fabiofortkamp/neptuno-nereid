# Nereid — Specification

Nereid is a local-first task manager, the first app of the **Neptuno** family of
productivity tools. It takes OmniFocus's model (folders, sequential/parallel
projects, defer and due dates) and adds two things OmniFocus lacks:
cross-project **dependencies** and a repeat model with **Check** and **Finish**.

Decisions behind this spec are recorded in [`docs/adr/`](docs/adr/).

## Goals

1. **Daily use decides.** Nereid must replace OmniFocus for its author. Portfolio
   value comes from that. ([ADR 0001](docs/adr/0001-daily-use-drives-scope.md))
2. **Portfolio of AI-assisted development.** The process (specs, ADRs, agent
   instructions, commit trail) is part of the product.
3. **System-ready, not system-built.** Keep a clean core and API so future
   Neptuno apps and AI agents can plug in. Build none of that yet.

Non-goals for now: AI features inside the app, multiple users, sharing.
Future AI path: MCP server/API → natural-language capture, both with evals.

## Glossary

Domain terms are defined in [`GLOSSARY.md`](GLOSSARY.md).

## Domain model

### Ownership and identity

Every record has a globally unique ID, an `ownerId` and change timestamps from
day one, so sync (v1.3) and multiple users (someday) need no migration.

- **IDs** are UUIDv7 (time-sortable).
- **`ownerId`** is one UUID generated on first run and kept in local config;
  there are no accounts.
- **Timestamps** are `createdAt` and `updatedAt`, ISO 8601 UTC strings.
- The core never generates IDs or reads the clock: both are injected, so tests
  use fixed values.

### Local data

The CLI keeps its data in `~/Library/Application Support/Nereid/`. The
`NEREID_DATA_DIR` environment variable overrides it (tests, development).

### CLI task references

`nereid available` lists tasks oldest first, numbered, with their IDs.
Commands that take a task (e.g. `nereid complete`) accept either the number
from that freshly computed list or a task ID.

### States

- **Task:** `active` → `completed` | `dropped`.
  For a repeating task, Check keeps it `active` and logs an occurrence; Finish
  sets it `completed`.
- **Project:** `active` | `onHold` | `completed` | `dropped`.
  `onHold` hides all its tasks from Available.

### Availability

A task is **Available** when all hold:

1. It is `active`.
2. Its defer date is empty or in the past.
3. Its project is `active`, or it is in the Inbox.
4. If its project is sequential, it is the first unfinished task.
5. Every prerequisite is `completed` or `dropped`.

A task is **Blocked** when rules 1–4 hold and rule 5 fails.
Sequential order and dependencies both apply. A prerequisite inside an on-hold
project keeps its dependents blocked. Overdue tasks remain Available.

### Dependencies ([ADR 0009](docs/adr/0009-dependencies-availability-only.md))

- Many-to-many, across projects.
- Affect availability only. The model reserves an optional lag per dependency;
  no UI in v1.
- Dropping a prerequisite unblocks its dependents.
- Cycles are rejected.
- UX: a **"Waiting on…"** field (searchable picker), an **"Add follow-up"**
  action that creates a dependent task, **badges** ("waiting on N",
  "unblocks N") and a **Blocked** view. Tasks without dependencies look like
  any simple todo item.

### Repeats ([ADR 0010](docs/adr/0010-repeat-model-check-finish.md))

- Rules are stored as RFC 5545 **RRULE**; the engine accepts any valid rule.
- Two schemes: **from completion** (next = Check date + interval) and
  **from schedule** (next = previous scheduled date per rule).
- **Check** logs an occurrence (kept as history) and advances the defer date.
- **Finish** completes the task for good.
- Dependents stay blocked until Finish.
- Due date target per repeating task: **occurrence** (default, advances with
  each Check) or **series** (fixed deadline for Finish). The model allows both
  at once; v1 UI exposes one. *Revisit the default after real use.*
- v1 UI offers presets (every N days/weeks/months, monthly on day X) and
  nth-weekday rules ("2nd Tuesday", "last Friday").

## Architecture

- **TypeScript everywhere** ([ADR 0003](docs/adr/0003-typescript-everywhere.md)),
  run natively by Node with no build step; literal unions instead of enums
  ([ADR 0011](docs/adr/0011-native-typescript-no-build.md)).
- **Toolchain:** Node 26 and pnpm 10 pinned in the repo's `mise.toml`;
  ESLint + Prettier; Vitest; GitHub Actions CI (typecheck, test, lint, format).
- **Local-first** with a self-hosted sync server
  ([ADR 0002](docs/adr/0002-local-first-self-hosted-sync.md)).
- **TinyBase** for storage and sync, behind a repository interface owned by the
  core ([ADR 0004](docs/adr/0004-tinybase-behind-repository.md)).
- **Node + Ink** for the TUI ([ADR 0005](docs/adr/0005-node-ink-tui.md)).
- **pnpm monorepo** ([ADR 0006](docs/adr/0006-pnpm-monorepo.md)):

```
packages/core/     domain rules: tasks, projects, availability, dependencies, repeats (pure, no I/O)
packages/storage/  repository implementations: JSON file (v0.1), TinyBase (v0.2+)
apps/cli/          v0.1 bare CLI → v1.0 Ink TUI
apps/server/       v1.3 sync server (created when that version starts)
apps/mac/          v1.4 Tauri app (created when that version starts)
```

## Roadmap

| Version | Theme | Scope |
|---|---|---|
| v0.1 | Walking skeleton | Core + tests + bare CLI, Inbox only: add a task, complete a task, list Available; JSON-file storage |
| v0.2 | Real storage | TinyBase replaces the JSON file behind the repository; no feature changes, no v0.1 data migration |
| v0.3 | Drop | Drop a task |
| v0.4 | Defer dates | Defer dates (availability rule 2) |
| v0.5 | Projects | Parallel projects and project status, including `onHold` (rule 3); move tasks out of the Inbox |
| v0.6 | Sequential projects | Sequential projects (rule 4) |
| v1.0 | Daily driver, one Mac | Ink TUI; folders, due dates, quick capture, Forecast view |
| v1.1 | Dependencies | Waiting on…, Add follow-up, badges, Blocked view, cycle rejection |
| v1.2 | Repeats | Check/Finish, presets + nth-weekday, occurrence/series due |
| v1.3 | Sync | Self-hosted TinyBase sync server; two Macs in sync |
| v1.4 | macOS app | Tauri UI on the same core |
| v1.5 | Comfort | Notes, OmniFocus import, review cycles |
| v2.0 | iPhone | Mobile capture and checking |

Each version is one step: when v0.x drags, merge steps rather than add more
(ADR 0001). Versions are tracked as GitHub milestones; issue titles name
capabilities, not versions.

Out of v1.x: subtasks, tags/contexts, custom perspectives, attachments,
notifications, flags, time estimates.

## Process

- `AGENTS.md`, this spec and ADRs are the agents' source of truth.
- One prompt = one commit, with tests, 50/72 messages, `Co-Authored-By` trailers.
- From v1.0, a blog series on the author's website (needs the website revived
  first; tracked outside this repo).

## Open items

- Verify TinyBase's current docs (synchronizer, SQLite persister, Node support)
  at the start of v0.2; the initial survey had unconfirmed details. Choose
  the persister (file or SQLite) then.
- Revisit the occurrence-due default after real use.
- v0.4: are defer dates instants or calendar dates? Leaning: instants (ISO
  UTC), with date-only input meaning local start of day.
- v0.6: rule 4 says "first unfinished task", but **Finish** is a repeat term.
  Leaning: "the first `active` task in project order", with a glossary note.
  Also confirm that a deferred or Blocked first task holds back the rest.

## Changelog

- 2026-11-25: Initial spec from the design grilling session: goals, glossary,
  domain model, availability rules, dependencies, repeats, architecture,
  roadmap. ADRs 0001–0010 added. No code yet.
- 2026-11-25: Claimed the `@neptuno` npm scope (free organization).
- 2026-11-25: Second grilling session. Glossary moved to `GLOSSARY.md`.
  Roadmap split into v0.1–v0.6 single-concept steps (JSON storage first,
  TinyBase in v0.2). Identity, local data dir, CLI task references and
  toolchain specified. ADR 0011 added. Versions tracked as GitHub milestones.
