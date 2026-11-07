# ADR-0002: Frontend Testing Paradigm and Agentic Verification

## Status
Accepted

## Date
2026-11-07

## Context
Shopizy SPA is an Angular 22 Single Page Application built on Standalone Components, reactive state management (Angular Signals and RxJS), real-time SignalR WebSocket telemetry, and PCI-sensitive Stripe Elements checkout. Development and maintenance are carried out collaboratively by human engineers and autonomous AI coding agents operating at high velocity.

Without an explicit testing paradigm and verification contract:
- AI agents and developers may over-index on heavy, slow end-to-end browser tests or write brittle unit tests that verify implementation ceremony rather than functional behavior.
- Test suites risk exposing sensitive payment data or attempting to interact directly with secure third-party iframes (such as Stripe Elements), violating PCI-DSS constraints.
- Test failures become difficult to diagnose if test names are overly generic or terse (violating DAMP principles).
- Agentic UI changes may be committed without actual runtime browser verification, leading to visual regressions and layout breakage.

We require a formalized testing paradigm, distribution ratio, and agentic verification protocol grounded in Test-Driven Development (TDD) best practices.

## Decision

We establish the following testing paradigm and architectural invariants across `shopizy-app`:

### 1. Frontend Testing Distribution (~75% Unit/Component, ~25% E2E)
We adopt a pragmatic testing pyramid tailored to modern Angular SPAs:

```
          ┌─────────────────────────┐
          │   Playwright E2E (~25%)  │  Multi-Persona Browser Journeys
          │  Customer / Admin / Guest│  (Critical flows, auth, checkout)
          ├─────────────────────────┴─────────┐
          │     Unit & Component Tests (~75%)  │  Standalone Components, Signals,
          │   Karma / Jasmine / Angular TestBed│  RxJS Transforms, Interceptors, Guards
          └───────────────────────────────────┘
```

- **~75% Unit & Component Tests (`npm test`)**:
  - **Scope**: Angular Standalone component logic, Angular Signals, RxJS pipe transforms (e.g., search debounce, error transformations, calculation logic), HTTP interceptors (JWT injection, 401 silent token refresh queue), and route guards (`AuthGuard`, role-based access).
  - **Tooling**: Karma, Jasmine, Angular `TestBed`, `provideHttpClientTesting()`, and centralized test spies (`src/app/testing/test-helpers.ts`).
  - **Speed & Isolation**: In-memory, headless execution executing under seconds without external network dependencies.

- **~25% End-to-End Tests (`npm run test:e2e`)**:
  - **Scope**: Critical user journeys spanning multi-page workflows, role permissions, cart drawer state transitions, and checkout flows.
  - **Tooling**: Playwright with multi-persona storage state architecture (`setup-user`, `setup-admin`, `user-chrome`, `admin-chrome`, `guest-chrome`).
  - **Persona Isolation**: Authentication state is captured once into `playwright/.auth/*.json` and reused across tests, avoiding redundant login overhead while exercising realistic role permissions.

### 2. Core Testing Invariants

#### A. State Over Interaction Testing
- Tests must assert observable state outcomes, Angular Signals, rendered DOM states, or emitted events rather than internal method calls, private properties, or DOM click ceremony.
- Do not assert that a private helper method was called 3 times; assert that the component's state or rendered template reflects the final expected data.
- Avoid tight coupling to template structure; prefer semantic queries (`getByRole`, `getByText`, or stable component attributes) over brittle CSS hierarchical paths (`div > div:nth-child(2) > span`).

#### B. Zero Cardholder Data in Test Fixtures (PCI-DSS & Stripe Elements)
- Strictly enforce zero-cardholder-data in all test fixtures, mock data, and test environments.
- **Never** inject real or synthetic Primary Account Numbers (PANs), CVVs, or expiration dates into Angular component inputs, forms, or network mocks.
- **Unit Testing**: Stripe Elements components and payment services must be isolated using mock modules (`NgxStripeModule.forRoot('pk_test')`) and typed spies (`createPaymentApiSpy()`). Unit tests verify that client secrets and payment intent IDs are processed correctly, without instantiating or mounting real Stripe payment fields.
- **E2E Testing**: Checkout flows must use tokenized test payment intents or alternative payment methods (e.g. Cash on Delivery / mock intents) without attempting to bypass or scrape the Stripe iframe sandbox.

#### C. DAMP Test Naming (Descriptive And Meaningful Phrases)
- Adopt DAMP (Descriptive And Meaningful Phrases) over DRY (Don't Repeat Yourself) for test specifications and block names.
- Test descriptions must clearly state:
  1. The target condition or action.
  2. The expected outcome or state change.
  3. The specific scenario or boundary context.
  - *Example*: `it('should queue concurrent requests and retry with new bearer token upon successful 401 silent refresh', ...)` instead of `it('handles 401', ...)`.
- Tests serve as live documentation; when a test fails in CI or in an agent's terminal, the failure message alone should pinpoint the broken requirement.

#### D. Chrome DevTools Verification Workflow for Agentic UI Debugging
Autonomous agents modifying UI components, layouts, or stylesheets must follow a runtime verification workflow using browser inspection tools (such as Chrome DevTools or Playwright UI trace):
1. **DOM & Responsive Layout Inspection**: Inspect the component DOM tree and verify responsiveness across desktop and mobile viewports (e.g. mobile cart drawer, faceted filter panel).
2. **Console Telemetry**: Verify zero uncaught JavaScript exceptions, unhandled Promise rejections, or Angular hydration/binding errors.
3. **Network Telemetry**: Verify API requests send correct Authorization headers, handle 401/403 responses gracefully, and avoid duplicate/redundant fetches.
4. **State & Signal Inspection**: Verify reactive Signals and RxJS observables resolve to valid data states without infinite change detection loops.

### 3. TDD Loop Discipline
When adding features, modifying state services, or resolving bugs:
- **Red**: Author a failing unit or E2E test that captures the requirement or reproduces the bug (Prove-It pattern).
- **Green**: Implement the minimal code change to pass the test.
- **Refactor**: Clean up and optimize while keeping all test suites green.

## Consequences

### Positive
- Predictable and fast test execution: ~75% fast unit tests provide immediate local feedback, while ~25% Playwright tests safeguard end-to-end user journeys.
- Elimination of brittle tests: State-focused assertions withstand UI refactorings without unnecessary test rewrite cycles.
- PCI-DSS compliance assurance: Guaranteed absence of sensitive card data in version control and test logs.
- High agent autonomy: DAMP naming and Chrome DevTools verification workflows empower AI agents to detect, localize, and resolve regressions autonomously.

### Negative / Trade-offs
- Maintaining mock fixtures and spies in `test-helpers.ts` requires discipline whenever API contracts evolve.
- Playwright multi-persona suites require running backend API services or local mock servers for full E2E execution.
