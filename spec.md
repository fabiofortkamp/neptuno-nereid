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

| Term | Meaning |
|---|---|
| **Folder** | Container for projects and other folders |
| **Project** | Container for tasks; *sequential* or *parallel* |
| **Inbox** | Tasks not yet assigned to a project |
| **Defer date** | Before this date the task is not available |
| **Due date** | Deadline; past it the task is *overdue* (a flag, not a state) |
| **Dependency** | Task B *waits on* task A: B is blocked until A is completed or dropped |
| **Prerequisite** | A task another task waits on |
| **Check** | Complete one occurrence of a repeating task; the task stays active and its dates advance |
| **Finish** | Complete a repeating task for good; it stops repeating and unblocks its dependents |
| **Occurrence due** | Due date of each occurrence; advances on Check (default) |
| **Series due** | Deadline for Finish; never advances |
| **Available** | Can be worked on now (see rules below) |
| **Blocked** | Would be available except for an unfinished prerequisite |

## Domain model

### Ownership and identity

Every record has a globally unique ID, an `ownerId` and change timestamps from
day one, so sync (v1.3) and multiple users (someday) need no migration.

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

- **TypeScript everywhere** ([ADR 0003](docs/adr/0003-typescript-everywhere.md)).
- **Local-first** with a self-hosted sync server
  ([ADR 0002](docs/adr/0002-local-first-self-hosted-sync.md)).
- **TinyBase** for storage and sync, behind a repository interface owned by the
  core ([ADR 0004](docs/adr/0004-tinybase-behind-repository.md)).
- **Node + Ink** for the TUI ([ADR 0005](docs/adr/0005-node-ink-tui.md)).
- **pnpm monorepo** ([ADR 0006](docs/adr/0006-pnpm-monorepo.md)):

```
packages/core/     domain rules: tasks, projects, availability, dependencies, repeats (pure, no I/O)
packages/storage/  repository interface + TinyBase implementation
apps/cli/          v0.1 bare CLI → v1.0 Ink TUI
apps/server/       v1.3 sync server (created when that version starts)
apps/mac/          v1.4 Tauri app (created when that version starts)
```

## Roadmap

| Version | Theme | Scope |
|---|---|---|
| v0.1 | Walking skeleton | Core + tests + bare CLI: create task/project, list Available |
| v1.0 | Daily driver, one Mac | Ink TUI, local storage; folders, projects (seq/par), tasks, defer/due, Inbox + quick capture, Available and Forecast views |
| v1.1 | Dependencies | Waiting on…, Add follow-up, badges, Blocked view, cycle rejection |
| v1.2 | Repeats | Check/Finish, presets + nth-weekday, occurrence/series due |
| v1.3 | Sync | Self-hosted TinyBase sync server; two Macs in sync |
| v1.4 | macOS app | Tauri UI on the same core |
| v1.5 | Comfort | Notes, OmniFocus import, review cycles |
| v2.0 | iPhone | Mobile capture and checking |

Out of v1.x: subtasks, tags/contexts, custom perspectives, attachments,
notifications, flags, time estimates.

## Process

- `AGENTS.md`, this spec and ADRs are the agents' source of truth.
- One prompt = one commit, with tests, 50/72 messages, `Co-Authored-By` trailers.
- From v1.0, a blog series on the author's website (needs the website revived
  first; tracked outside this repo).

## Open items

- Verify TinyBase's current docs (synchronizer, SQLite persister, Node support)
  before writing storage code; the initial survey had unconfirmed details.
- Revisit the occurrence-due default after real use.

## Changelog

- 2026-11-25: Initial spec from the design grilling session: goals, glossary,
  domain model, availability rules, dependencies, repeats, architecture,
  roadmap. ADRs 0001–0010 added. No code yet.
- 2026-11-25: Claimed the `@neptuno` npm scope (free organization).
