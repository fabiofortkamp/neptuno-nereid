# 0009. Dependencies affect availability only

- Status: Accepted
- Date: 2026-11-25

## Context

Any task may wait on several others, in any project. The risk is a complex UI.

## Decision

A dependency means: the dependent is **blocked until every prerequisite is
completed or dropped**. No date math in v1; the model reserves an optional lag
per dependency. Cycles are rejected.

UX: a "Waiting on…" picker, an "Add follow-up" action, "waiting on N" /
"unblocks N" badges, and a Blocked view. Drag-to-link, capture syntax and a
graph view are deferred.

## Consequences

- "Follow up in a week" needs a manual defer date for now.
- Tasks without dependencies look like plain todo items.
- Cycle checks live in the core, not in storage (see ADR 0004).
