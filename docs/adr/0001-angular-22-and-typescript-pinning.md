# ADR-0001: Angular 22 Upgrade and TypeScript 6 Pinning

## Status
Accepted

## Date
2026-11-07

## Context
During the repository-wide modernization, frontend dependencies in Shopizy SPA were updated. `@angular/core` and the Angular CLI suite were upgraded to v22.

During compilation, two critical constraints emerged:
1. `@angular/compiler-cli` v22 requires TypeScript `>=6.0.0 and <6.1.0` (specifically `~6.0.2`). Attempting to run with TypeScript 7 causes compiler failure (`The Angular Compiler requires TypeScript >=6.0.0 and <6.1.0 but 7.x.x was found`).
2. Angular's TypeScript configuration deprecates non-relative path aliases without `baseUrl`. Path mappings must be specified as `"@app/*": ["./src/app/*"]`.

## Decision
1. **Pin TypeScript**: Lock `typescript` to `~6.0.2` in `package.json`.
2. **Update Path Aliases**: Configure `tsconfig.json` path mappings with explicit `./` prefixes.
3. **Document Invariant**: Record this rule in `AGENTS.md` and `docs/ARCHITECTURE.md` to prevent automated tools from upgrading TypeScript beyond v6.0.x until the Angular framework officially supports TS 7+.

## Consequences
- Clean, fast Angular builds via the Vite-based development and application builder.
- Stability across all Standalone components, RxJS/Signal state stores, and Playwright E2E suites.
