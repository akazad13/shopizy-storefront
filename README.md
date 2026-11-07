<div align="center">

# 🛍️ Shopizy SPA

### Modern, responsive e-commerce Single Page Application (SPA) built with Angular Standalone, Tailwind CSS, Stripe, and SignalR

[![Angular](https://img.shields.io/badge/Angular-22.2-DD0031?style=flat-square&logo=angular&logoColor=white)](https://angular.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-6.0-3178C6?style=flat-square&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-3.4-38B2AC?style=flat-square&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![Playwright](https://img.shields.io/badge/Testing-Playwright%20E2E-2EAD33?style=flat-square&logo=playwright&logoColor=white)](https://playwright.dev/)
[![Stripe](https://img.shields.io/badge/Payments-Stripe-008CDD?style=flat-square&logo=stripe&logoColor=white)](https://stripe.com/)
[![SignalR](https://img.shields.io/badge/WebSockets-SignalR-512BD4?style=flat-square&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)](LICENSE)

[Overview](#-overview) •
[Features](#-key-features) •
[Architecture & Decision Records](#-architecture--decision-records) •
[Tech Stack](#-tech-stack) •
[Repository Layout](#-repository-layout) •
[Quick Start](#-quick-start) •
[Testing](#-testing) •
[Documentation](#-documentation)

</div>

---

## 📌 Overview

**Shopizy SPA** is a high-performance customer storefront and administrative management application powering the Shopizy e-commerce ecosystem. Built with Angular Standalone Architecture, Tailwind CSS, Stripe Elements, and Microsoft SignalR, it provides a seamless shopping experience with real-time order tracking, reactive cart management, and multi-persona authentication.

### Key Highlights
- 🛍️ **Reactive Shopping Experience**: Instant cart updates, faceted catalog filtering (category trees, brands, price ranges), debounced search, and wishlist integration.
- 💳 **Stripe Elements Checkout**: Multi-step checkout with saved shipping addresses, dynamic carrier estimates, and client-secret authorized Stripe card validation.
- 📡 **Real-Time Order Telemetry**: Live order tracking status updates (`Pending` -> `Confirmed` -> `Shipped` -> `Delivered`) and admin KPI metrics pushed via SignalR WebSockets.
- ⚡ **Admin Management Suite**: Full product CRUD, category hierarchy editor, order fulfillment operations, and customer analytics.
- 🎭 **Multi-Persona E2E Automation**: Playwright test suites validating Customer, Admin, and Guest journeys with cached auth states.

---

## 🏛 Architecture & Decision Records

```mermaid
flowchart LR
    subgraph SPA["Shopizy SPA (Angular Standalone)"]
        Store["Customer Storefront"]
        AdminPortal["Admin Dashboard"]
        State["RxJS Services (Cart, Wishlist, Auth)"]
    end

    subgraph Backend["Backend API (.NET 10)"]
        API["Shopizy.Api REST Endpoints"]
        Hub["SignalR WebSocket Hubs"]
    end

    subgraph External["External Gateways"]
        StripePay["Stripe Elements / API"]
    end

    Store --> State
    AdminPortal --> State
    State -->|HTTP REST + Bearer Token| API
    State <-->|WebSockets| Hub
    Store -.->|Card Tokenization| StripePay
```

> [!NOTE]
> HTTP interceptors manage silent JWT refresh on 401 responses, ensuring uninterrupted browsing and administrative sessions.

### Architecture Decision Records (ADRs)

Architectural choices, non-obvious engineering trade-offs, and critical constraints are formally recorded and tracked as Architecture Decision Records (ADRs) in accordance with the repository's documentation standards.

See the full index and governance guidelines in [**docs/adr/README.md**](docs/adr/README.md).

| ADR | Title | Status | Date | Area |
| :--- | :--- | :--- | :--- | :--- |
| [ADR-0000](docs/adr/0000-record-architecture-decisions.md) | Record Architecture Decisions | Accepted | 2026-11-07 | Architecture & Documentation |
| [ADR-0001](docs/adr/0001-angular-22-and-typescript-pinning.md) | Angular 22 Upgrade and TypeScript 6 Pinning | Accepted | 2026-11-07 | Framework & Tooling |
| [ADR-0002](docs/adr/0002-testing-paradigm-and-agentic-verification.md) | Frontend Testing Paradigm and Agentic Verification | Accepted | 2026-11-07 | Testing & Quality Assurance |

---

## 🛠 Tech Stack

| Domain | Technologies |
| :--- | :--- |
| **Framework** | Angular (Standalone Components, Signals, Reactive Forms, Route Resolvers) |
| **Styling & UI** | Tailwind CSS, Custom SVG Sprite Sheet system |
| **State Management** | RxJS reactive streams and behavior subjects |
| **Real-time WebSockets** | `@microsoft/signalr` |
| **Payment Gateway** | `ngx-stripe` (Stripe Elements) |
| **Authentication** | `@auth0/angular-jwt` (JWT token decoding and expiration handling) |
| **Testing** | Playwright (E2E), Karma & Jasmine (Unit tests) |

---

## 📂 Repository Layout

```text
├── e2e/                           # Playwright E2E suites (customer, admin, guest personas)
├── docs/                          # In-depth architectural & testing documentation
│   ├── adr/                       # Architecture Decision Records (ADRs)
│   │   ├── 0000-record-architecture-decisions.md
│   │   ├── 0001-angular-22-and-typescript-pinning.md
│   │   └── README.md              # ADR index and lifecycle documentation
│   ├── ARCHITECTURE.md            # Technical design, data flows, and interceptors
│   └── E2E_TESTING_GUIDE.md       # Playwright configuration and multi-persona setup
├── public/                        # Static assets and icons.svg sprite sheet
├── src/
│   ├── app/
│   │   ├── api/                   # Typed REST API service layer
│   │   ├── components/            # Standalone UI components (Storefront, Admin, Auth)
│   │   ├── guards/                # Route guards (AuthGuard with role verification)
│   │   ├── interceptors/          # Interceptors (Bearer token, 401 Silent Refresh, Errors)
│   │   ├── models/                # Domain models, entities, and TypeScript DTOs
│   │   ├── resolvers/             # Route data pre-fetching resolvers
│   │   └── services/              # State & business services (Cart, Wishlist, SignalR)
│   ├── environments/              # Environment configurations
│   └── styles.css                 # Tailwind CSS directives
├── playwright.config.ts           # Playwright test configuration
└── package.json                   # Dependencies and scripts
```

---

## 🚀 Quick Start

### Prerequisites
- Node.js 20+
- npm 9+
- Running backend API (e.g. `http://localhost:18080`)

### 1. Clone & Install
```bash
git clone https://github.com/akazad13/shopizy-spa.git
cd shopizy-spa
npm install
```

### 2. Configure Environment
Update API endpoint in `src/environments/environment.development.ts`:
```typescript
export const environment = {
  apiUrl: 'http://localhost:18080',
  production: false
};
```

### 3. Start Development Server
```bash
npm start
```
Navigate to `http://localhost:4200/`.

---

## 🧪 Testing

Shopizy SPA enforces a comprehensive testing paradigm governed by [**ADR-0002**](docs/adr/0002-testing-paradigm-and-agentic-verification.md) with an intentional distribution ratio:
- **~75% Unit & Component Tests**: Angular Standalone components, Signals, RxJS pipe transforms, HTTP auth interceptors, and route guards via Karma/Jasmine and Angular `TestBed`.
- **~25% End-to-End Tests**: Playwright browser automation with multi-persona authentication (`Customer`, `Admin`, `Guest`) validating real critical workflows without repetitive login steps.

### Testing Invariants
1. **State Over Interaction**: Assert state changes, Signals, and emitted data rather than internal method spies or brittle DOM hierarchies.
2. **Zero Cardholder Data**: Strictly PCI-DSS compliant. Never use real/synthetic PANs or CVVs in test fixtures. Stripe Elements is mocked and isolated via `NgxStripeModule.forRoot('pk_test')` in unit tests and tokenized flows in E2E.
3. **DAMP Test Naming**: Descriptive And Meaningful Phrases for clear, diagnosis-ready failure reports.
4. **Chrome DevTools Verification**: Agentic UI debugging with live DOM inspection, zero console errors, and network telemetry.

### Test Commands

| Test Suite | Scope | Command |
| :--- | :--- | :--- |
| **Unit Tests (Watch)** | Interactive unit test runner with file watching | `npm test` |
| **Unit Tests (Single Run)** | Headless, single-run execution for CI & agent validation | `npm test -- --watch=false` |
| **Code Linting** | ESLint static code analysis across TypeScript & HTML | `npm run lint` |
| **All E2E Tests** | Headless Playwright tests across all personas | `npm run test:e2e` |
| **Interactive E2E (UI Mode)** | Time-travel debugging, DOM inspection, and trace viewer | `npm run test:e2e:ui` |
| **Customer Journey** | Authenticated customer browsing, cart, and checkout flows | `npx playwright test --project=user-chrome` |
| **Admin Journey** | Admin dashboard, product CRUD, and fulfillment flows | `npx playwright test --project=admin-chrome` |
| **Guest Journey** | Public catalog, search, filtering, and guest cart flows | `npx playwright test --project=guest-chrome` |

---

## 📚 Documentation

| Guide | Focus |
| :--- | :--- |
| 🏛️ [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) | Standalone routing, JWT silent refresh interceptors, and state architecture |
| 📋 [docs/adr/README.md](docs/adr/README.md) | Architecture Decision Records (ADRs) catalog and governance |
| 🧪 [docs/E2E_TESTING_GUIDE.md](docs/E2E_TESTING_GUIDE.md) | Multi-persona Playwright test fixtures and auth setup |
| 🎨 [ICONS_GUIDE.md](ICONS_GUIDE.md) | Built-in SVG sprite catalog and `<app-icon>` usage |

---

## 🤖 AI-Driven Development & Contributing

Autonomous coding agents (Antigravity, Cursor, Claude Code, GitHub Copilot) operating in this repository must consult [AGENTS.md](AGENTS.md) for Angular standalone component patterns, zero-secret environment guidelines, and Playwright multi-persona E2E requirements.

---

## 👤 Author

**Md. Abul Kalam**
- GitHub: [@akazad13](https://github.com/akazad13)
- LinkedIn: [Md. Abul Kalam](https://linkedin.com/in/akazad13)

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
