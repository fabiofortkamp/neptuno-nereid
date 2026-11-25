# 0005. Node + Ink for the TUI

- Status: Accepted
- Date: 2026-11-25

## Context

v1.0 is terminal-only. Candidates: Ink (React for terminals, on Node) and
OpenTUI (requires Bun).

## Decision

Use **Node** as the runtime and **Ink** for the TUI.

## Consequences

- A mature, widely used stack (Claude Code is built on Ink).
- React skills learned here transfer to the Tauri UI in v1.4.
- Bun can be reconsidered later at little cost.
