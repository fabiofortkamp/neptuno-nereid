# 0004. TinyBase behind a repository interface

- Status: Accepted
- Date: 2026-11-25

## Context

A November 2026 survey of TypeScript local-first libraries left three
candidates: TinyBase, PowerSync and LiveStore. Triplit, cr-sqlite and
Replicache were dead or frozen; Zero rejects offline writes; Jazz was mid-
rewrite; InstantDB's cloud is shutting down.

- **TinyBase:** MIT, tiny self-hosted sync server, runs on Node, Tauri, Expo and
  browsers; per-cell last-write-wins merge; no built-in auth.
- **PowerSync:** relational SQLite, permissions; needs Postgres + service +
  write API; server under FSL; Tauri support in alpha.
- **LiveStore:** event-sourced SQLite; pre-1.0; self-hosting needs extra work.

## Decision

Use **TinyBase** for local storage and (from v1.3) sync. The core never imports
TinyBase: it depends on a **repository interface** it owns, implemented in
`packages/storage`.

## Consequences

- Fastest path to v1.0 with the least to learn.
- Invariants a field-level merge can't keep (e.g. no dependency cycles) are
  enforced by the core.
- Auth for multiple users must be added by us. If that stage arrives,
  PowerSync is the fallback; only `packages/storage` changes.
- TinyBase's current docs must be verified before storage code is written.
