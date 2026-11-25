# Domain Docs

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

## Before exploring, read these

- **The `## Glossary` section of `spec.md`**: the canonical glossary for now. If a root `GLOSSARY.md` is created later, it supersedes that section (and `spec.md` should point to it).
- **`docs/adr/`**: read ADRs that touch the area you're about to work in.

If any of these don't exist, **proceed silently**. Don't flag their absence; don't suggest creating them upfront. The `/domain-modeling` skill (reached via `/grill-with-docs` and `/improve-codebase-architecture`) creates them lazily when terms or decisions actually get resolved.

## File structure

Single-context repo (one domain, even though it is a pnpm monorepo):

```
/
├── spec.md                ← goals, glossary, domain model, roadmap
├── docs/adr/
│   ├── 0001-daily-use-drives-scope.md
│   └── ...
├── packages/
│   ├── core/
│   └── storage/
└── apps/
```

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in the glossary. Don't drift to synonyms the glossary explicitly avoids.

If the concept you need isn't in the glossary yet, that's a signal: either you're inventing language the project doesn't use (reconsider) or there's a real gap (note it for `/domain-modeling`).

## Flag ADR conflicts

If your output contradicts an existing ADR, surface it explicitly rather than silently overriding:

> _Contradicts ADR-0004 (TinyBase behind repository), but worth reopening because…_
