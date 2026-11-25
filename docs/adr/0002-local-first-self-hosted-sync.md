# 0002. Local-first with self-hosted sync

- Status: Accepted
- Date: 2026-11-25

## Context

A task app must capture anywhere, including offline. The app is single-user
for now, but may later be shared with friends and sold.

## Decision

Nereid is **local-first**: every device holds a full copy and works offline;
changes sync when connected. Sync runs through a **self-hosted** server (small
VPS, Docker). The sync technology must be fully self-hostable. Shared-file sync
(e.g. iCloud Drive) is rejected because it cannot grow into multi-user.

Every record carries a unique ID, an `ownerId` and change timestamps from day
one.

## Consequences

- Concurrent offline edits need a conflict strategy.
- No vendor lock-in; the author runs and backs up the server.
- Sync itself is deferred to v1.3, but the data shape is sync-ready from v0.1.
