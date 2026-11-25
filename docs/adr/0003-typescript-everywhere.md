# 0003. TypeScript everywhere

- Status: Accepted
- Date: 2026-11-25

## Context

The core must run in a terminal app (v1.0), a macOS app (v1.4), an iPhone app
(v2.0) and possibly a browser. The author is most fluent in Python.

Options considered:

- **Python:** good TUIs, but no viable path to native Apple apps or the
  browser; a local Django server cannot run on iOS.
- **Rust core + SwiftUI shells:** strongest native story, but two new
  languages plus FFI plumbing delay daily use.
- **TypeScript:** one language for core, TUI, desktop (Tauri), mobile and web;
  the strongest local-first library ecosystem.

## Decision

Use **TypeScript** for the core and all clients. The Mac app uses **Tauri**
(v1.4).

## Consequences

- The author learns TypeScript, starting on pure domain logic (v0.1).
- The Mac app is a web UI in a native shell, not SwiftUI.
- One core is shared by every client.
