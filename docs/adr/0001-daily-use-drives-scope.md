# 0001. Daily use drives scope

- Status: Accepted
- Date: 2026-11-25

## Context

Nereid has three goals that pull apart: a portfolio piece showing AI-assisted
engineering, a tool that replaces OmniFocus in daily use, and the first app of
a wider productivity system (Neptuno).

## Decision

When goals conflict, **daily use wins**. Portfolio value comes from an app the
author actually lives in. The "system" goal is only an architectural
constraint: a clean core and API so other tools can plug in later. Nothing of
the system is built now. The first versions contain no AI features; the AI
engineering is in how the app is built.

## Consequences

- Scope choices favor reliability and quick capture over demo appeal.
- The core stays UI- and storage-agnostic so future apps and agents can use it.
- AI features (MCP/API, natural-language capture, with evals) are deferred.
