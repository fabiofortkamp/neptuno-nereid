# Nereid

A local-first task manager, the first app of the **Neptuno** family.

Nereid follows OmniFocus's model (folders, sequential and parallel projects,
defer and due dates) and adds two things:

- **Dependencies:** a task can wait on several others, across projects.
- **Check and Finish:** a repeating task can be *checked* (log this
  occurrence and repeat) or *finished* (done for good, and its dependents
  become available).

## Status

Design phase. No code yet. See:

- [`spec.md`](spec.md): specification and roadmap
- [`docs/adr/`](docs/adr/): architecture decision records

## Stack (planned)

TypeScript, Node, Ink (TUI), TinyBase (local-first storage and sync), pnpm
workspaces; Tauri for the macOS app later.

## Setup

Not yet runnable. Setup and run instructions arrive with v0.1.

## License

[AGPL-3.0](LICENSE)
