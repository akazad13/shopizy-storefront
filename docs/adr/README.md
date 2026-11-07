# Architecture Decision Records (ADRs)

This directory documents the significant architectural and technical decisions made in the **Shopizy SPA** application. Capturing these decisions preserves the *why* behind technical designs, dependencies, and constraints for human engineers and autonomous AI agents.

---

## 📋 ADR Index

| ADR | Title | Status | Date | Area |
| :--- | :--- | :--- | :--- | :--- |
| [ADR-0000](0000-record-architecture-decisions.md) | Record Architecture Decisions | Accepted | 2026-11-07 | Architecture & Documentation |
| [ADR-0001](0001-angular-22-and-typescript-pinning.md) | Angular 22 Upgrade and TypeScript 6 Pinning | Accepted | 2026-11-07 | Framework & Tooling |

---

## 🔄 ADR Lifecycle

ADRs progress through standard lifecycle states:

```
PROPOSED ───► ACCEPTED ───► SUPERSEDED (by ADR-XXXX)
                   │
                   └───► DEPRECATED
```

- **Proposed**: Under review and actively discussed.
- **Accepted**: Approved, actively applied, and enforced as an architectural invariant.
- **Superseded**: Replaced by a newer decision (the earlier ADR remains intact for historical auditability).
- **Deprecated**: No longer applicable or discontinued.

---

## ✍️ Authoring a New ADR

When making a non-trivial architectural choice (e.g., changing state management libraries, altering payment gateways, reconfiguring authentication mechanisms, or altering build compilers):

1. Allocate the next sequential 4-digit number: `docs/adr/XXXX-title-in-kebab-case.md`.
2. Follow the standard template with headings:
   - `# ADR-XXXX: Title`
   - `## Status`
   - `## Date`
   - `## Context`
   - `## Decision`
   - `## Alternatives Considered` (if applicable)
   - `## Consequences`
3. Add the new ADR entry to the table in this file (`docs/adr/README.md`).
4. Cross-reference architectural invariants in [`AGENTS.md`](../../AGENTS.md) and [`docs/ARCHITECTURE.md`](../ARCHITECTURE.md).
