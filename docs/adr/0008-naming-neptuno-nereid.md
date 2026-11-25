# 0008. Naming: Neptuno family, Nereid app

- Status: Accepted
- Date: 2026-11-25

## Context

The app is the first of a family of tools with an astronomical theme. The
author's earlier brand idea was **Neptuno** (Neptune in Portuguese/Spanish).
"Triton" was considered for the app but collides with NVIDIA Triton and
OpenAI's Triton language in the AI-engineering space.

## Decision

**Neptuno** is the family brand; each app is named after a moon of Neptune.
This task app is **Nereid**: repo `neptuno-nereid`, packages under the
`@neptuno/` scope.

## Consequences

- Room to name future apps (Proteus, Larissa, Galatea, …).
- The npm name `neptuno` is taken by an empty package; the `@neptuno` scope
  must be claimed before publishing anything.
