# Nereid — Agent Instructions

Nereid is a local-first task manager (TypeScript, pnpm monorepo), the first
app of the Neptuno family. House rules from the home repo
(`~/home/CLAUDE.md`) apply; this file wins where they conflict.

## Read first

1. `spec.md` — goals, domain model, availability rules, roadmap.
2. `GLOSSARY.md` — domain terms.
3. `docs/adr/` — why each decision was made. Don't reverse an accepted ADR
   silently: propose a new ADR that supersedes it.

## Domain language

Use the glossary terms in code and docs: **Check** and **Finish** (repeating
tasks), **prerequisite**, **Waiting on**, **Available**, **Blocked**,
**occurrence due** vs **series due**. Don't invent synonyms.

## Architecture rules

- `packages/core` is pure TypeScript: no I/O, no TinyBase, no UI imports. It
  owns the repository interface.
- `packages/storage` implements that interface with TinyBase. Nothing else
  imports TinyBase.
- Domain invariants (no dependency cycles, valid state transitions) are
  enforced in the core.
- Every record has a unique ID, `ownerId` and change timestamps.
- Create `apps/server` and `apps/mac` only when their roadmap version starts.

## Workflow

- Read `spec.md` before coding; log changes in its Changelog after.
- Write tests first where practical (Vitest); all tests pass before commit.
- One prompt = one commit, 50/72 message format.
- New significant decisions get an ADR (`docs/adr/NNNN-title.md`).

## Interviews and grilling

When interviewing the author (grilling, design sessions, clarifying
questions), ask **one question at a time**, with a recommended answer, and
wait for the reply before asking the next. This overrides skills that batch
questions into rounds.

## Agent skills

### Issue tracker

Issues live in GitHub Issues on `fabiofortkamp/neptuno-nereid` (via `gh`). See `docs/agents/issue-tracker.md`.

### Domain docs

Single-context: `GLOSSARY.md` + `docs/adr/`. See `docs/agents/domain.md`.

## Status

Pre-code. Next: v0.1 walking skeleton (see `spec.md` roadmap and the v0.1
GitHub milestone), starting with the workspace scaffold. Commands will
be documented here once the workspace is scaffolded.
