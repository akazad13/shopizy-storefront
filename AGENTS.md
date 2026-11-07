# AGENTS.md — AI Agent Guidance & Repository Rules

This document provides mandatory guidance for autonomous AI coding agents (Antigravity, Claude Code, GitHub Copilot, Cursor, Windsurf, Devin) operating in `akazad13/shopizy-app`.

---

## 1. System Identity & Architecture

**Product**: `Shopizy SPA` — Customer storefront and admin management portal for the Shopizy e-commerce ecosystem.
- **Runtime**: Angular Standalone Architecture, TypeScript.
- **Styling**: Tailwind CSS v3 / v4 PostCSS build.
- **Payments**: Stripe Elements / `ngx-stripe` / `@stripe/stripe-js`.
- **Real-Time**: `@microsoft/signalr` for live order telemetry and merchant dashboard stats.
- **E2E Testing**: Playwright multi-persona browser automation (`Customer`, `Admin`, `Guest`).
- **Architectural Decisions**: All technical decisions and architectural invariants are codified in [**docs/adr/README.md**](docs/adr/README.md).

### Project Layout (`src/`):
- `src/app/core/`: Singleton services, HTTP auth interceptors, route guards (`auth.guard`, `admin.guard`).
- `src/app/shared/`: Reusable components (Navbar, Footer, ProductCard, Pagination, LoadingSpinner).
- `src/app/features/`: Feature modules:
  - `shop/`: Catalog browsing, faceted filtering, search, and product details.
  - `cart/`: Sliding reactive cart drawer and checkout review.
  - `checkout/`: Multi-step address selection, carrier selection, Stripe payment.
  - `orders/`: Order history and SignalR live status tracker.
  - `admin/`: Product management, inventory stock adjustment, fulfillment.
- `e2e/`: Playwright test suites and authentication fixtures.
- `docs/adr/`: Architecture Decision Records index and specifications.

---

## 2. Inviolable Architectural Invariants

Autonomous agents modifying this repository must strictly adhere to these invariants:

1. **Architecture Decision Records Governance ([ADR-0000](docs/adr/0000-record-architecture-decisions.md))**:
   - Any architectural paradigm shift, framework upgrade constraint, or external service pattern must be documented as an ADR in `docs/adr/` and cataloged in [docs/adr/README.md](docs/adr/README.md).
   - Invariants documented in existing ADRs must never be silently removed, bypassed, or weakened.
2. **Angular Standalone Standards**:
   - Use Standalone Components (`standalone: true`). Do not generate legacy `NgModule` wrappers.
   - Prefer Angular Signals and modern RxJS pipe patterns for state management.
3. **PCI-DSS Compliance & Cardholder Data Protection**:
   - Never capture, transmit, or store raw credit card numbers in Angular components, services, or local state.
   - All card payments must use Stripe Elements (`ngx-stripe` / `@stripe/stripe-js`) with client-secret authorization.
4. **Strict Peer Dependency Safeguard & TypeScript Version Pinning ([ADR-0001](docs/adr/0001-angular-22-and-typescript-pinning.md))**:
   - Always run `npm install --legacy-peer-deps`.
   - **TypeScript Pinning**: `@angular/compiler-cli` v22 strictly requires TypeScript `~6.0.2`. Do NOT upgrade `typescript` to `7.x` until Angular officially supports it.
   - Path aliases in `tsconfig.json` must be relative (`./src/app/*`).
5. **Zero-Secret & Environment Configuration**:
   - API endpoints and Stripe publishable keys must come strictly from `src/environments/environment.ts` and `environment.prod.ts`.
   - Never commit sensitive keys, live secrets, or customer data to the repository.
6. **Authentication & Token Lifecycle**:
   - JWT tokens must be managed through `TokenService` and attached via `AuthTokenInterceptor`.
   - Silent 401 token refresh queue must be maintained without race conditions or infinite loops.
   - Role-based route access must use `AuthGuard` with explicit role validation (`Admin`, `Customer`).
7. **Multi-Persona Testing Safeguards**:
   - Playwright E2E suites test distinct personas (`Customer`, `Admin`, `Guest`). Any route or permission change must verify these test fixtures pass without breaking authentication state caching.

---

## 3. Commands Cheat Sheet

```bash
# Install dependencies
npm install --legacy-peer-deps

# Start Angular dev server
npm start

# Run production build
npm run build

# Run unit tests
npm test -- --watch=false

# Run Playwright E2E tests
npx playwright test
```

---

## 4. Verification Gates for AI Agents

Before submitting code changes or finishing a turn:
1. Run `npm run build` — Angular AOT production build must succeed with **0 errors**.
2. Verify responsive layout for mobile checkout viewports.
3. Verify all architectural invariants and relevant ADRs in [docs/adr/README.md](docs/adr/README.md) are respected.
