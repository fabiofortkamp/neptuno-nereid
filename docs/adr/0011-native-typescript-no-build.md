# 0011. Native TypeScript, no build step, no enums

- Status: Accepted
- Date: 2026-11-25

## Context

Node 26 runs `.ts` files natively by stripping types. The author is new to
TypeScript, and a per-package `tsc` build (or a bundler) adds config and
watch-mode friction across the monorepo before any client needs compiled
output.

Type stripping only works for *erasable* syntax: enums, namespaces and
parameter properties need compilation.

## Decision

- Node runs TypeScript source directly; there is no `dist/`. Workspace
  packages point `exports` at `src/*.ts`.
- `tsc --noEmit` only typechecks. `tsconfig` sets `strict` and
  `erasableSyntaxOnly`.
- Fixed sets of values are **string literal unions**
  (`type TaskStatus = "active" | "completed" | "dropped"`), with an
  `as const` array when a runtime list is needed. No `enum`.

## Consequences

- Stored values are the domain values: a status is the string `"onHold"`,
  with no mapping layer between storage and core.
- `switch` statements get exhaustiveness checks through `never`.
- A build step is added only when a client needs it (the Tauri app in v1.4,
  or publishing the CLI to npm).
