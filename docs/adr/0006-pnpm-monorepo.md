# 0006. pnpm monorepo

- Status: Accepted
- Date: 2026-11-25

## Context

Several clients share one core. The author works solo with a one-prompt,
one-commit rule, and AI agents benefit from seeing the whole system.

## Decision

One repository, `neptuno-nereid`, managed with **pnpm workspaces**:
`packages/core`, `packages/storage`, `apps/cli`, and later `apps/server` and
`apps/mac`, each created only when its version starts.

## Consequences

- Features that touch core and a client land in one atomic commit.
- No cross-repo version juggling.
- pnpm's strict dependency rules catch undeclared imports.
- Other Neptuno apps would live in their own repos; sharing code across them is
  a later decision.
