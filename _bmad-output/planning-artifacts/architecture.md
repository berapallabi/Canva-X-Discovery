---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
inputDocuments: 
  - "_bmad-output/planning-artifacts/prd.md"
  - "_bmad-output/planning-artifacts/product-brief-Canva-X-Discovery-2026-03-23.md"
  - "_bmad-output/planning-artifacts/research/domain-canva-x-publishing-integration-research-2026-03-23.md"
  - "_bmad-output/planning-artifacts/research/canva-sdk-content-publisher-technical-research-2026-03-24.md"
workflowType: 'architecture'
project_name: 'Canva-X-Discovery'
user_name: 'Pallabi'
date: '2026-03-25T13:54:25+05:30'
lastStep: 8
status: 'complete'
completedAt: '2026-03-25'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
The FRs dictate a purely synchronous architecture.
**Synchronous Mode (Post Now):** The frontend coordinates exporting media via Canva SDK and immediately pushes it to the backend. The backend acts as a stateless proxy to X API for media delivery.
*Key Constraints:* Payload text limits (5KB) must be preflighted on the frontend. A pre-OAuth educational UI is required for trust. Multiple pages from Canva design are attached as flat images per post.

**Non-Functional Requirements:**
- **Security:** Requires strict JWT cryptographic verification of the Canva user session. The backend relies solely to securely proxy OAuth tokens managed natively by Canva.
- **Reliability:** The 99% publish success rate demands chunked media uploads to X.

**Scale & Complexity:**
The MVP targets 5,000 MAU.
- **Primary domain:** Frontend-heavy Platform Extension
- **Complexity level:** Low-Medium
- **Estimated architectural components:** 2 main repos (Canva Frontend App, Stateless Backend Proxy)

### Technical Constraints & Dependencies

- **Canva App Review:** Frontend strictly constrained to React + `@canva/app-ui-kit`. No custom CSS that breaks light/dark mode.
- **Payload Limits:** `PublishRef` object is capped at 5KB. Strict frontend validation is required.
- **X API Limits:** Free tier is limited to 500,000 tweets/month.

### Cross-Cutting Concerns Identified

1. **Idempotency & Retry Logic:** Network failures between Backend ↔ X API require a robust retry mechanism with a global circuit breaker.
2. **Session Management:** Securing the connection via Canva JWT to lookup backend X OAuth tokens securely.
3. **Observability:** Granular logging required to debug across the Canva Export step, the Backend intake step, and the X API delivery step.

## Starter Template Evaluation

### Primary Technology Domain
Frontend-heavy Platform Extension (Canva App SDK Frontend + Stateless Serverless Proxy Backend).

### Selected Starter: Canva CLI Content Publisher + Next.js (API Routes)

**Rationale for Selection:**
The Canva CLI is strictly required for the App UI Kit and SDK types. Since we pivoted to the "Zero Database" architecture (Canva handles OAuth statefully), our backend is simply a stateless proxy to handle X API requests (because X API lacks CORS). Next.js API Routes are the industry standard for lightweight, zero-maintenance stateless proxies.

**Initialization Command:**
```bash
# Frontend
npx @canva/cli create --template "content_publisher" my-canva-app

# Backend Proxy
npx create-next-app@latest my-stateless-proxy --typescript --eslint --no-src-dir --no-tailwind --app
```

**Architectural Decisions Provided by Starter:**
- **Language:** TypeScript 
- **Database:** None (Canva Native OAuth used).
- **Styling:** Canva App UI Kit (Frontend).
- **Code Organization:** Frontend grouped by intent UI. Backend is pure Serverless functions (`app/api/publish/route.ts`).
- **Development Experience:** Hot-reloading via `npm run dev`. Localhost tunneling (e.g., Ngrok) required.

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
- Dropped Custom Backend Database in favor of Canva's native OAuth storage.
- Standard REST API for cross-service communication.

**Deferred Decisions (Post-MVP):**
- Scheduling capabilities and Batch posting (which would require stateful database infrastructure).

### Data Architecture

- **Decision:** Zero Database Architecture (Stateless)
- **Rationale:** Rely entirely on Canva's native `auth` capability to securely store and refresh the X OAuth tokens. This eliminates the need for a PostgreSQL instance, lowering maintenance to near-zero.

### Authentication & Security

- **Decision:** Canva SDK `auth.initOauth()` + Canva JWT Verification
- **Rationale:** Offloading the OAuth flow to Canva ensures we don't handle token rotation. Our Next.js proxy will cryptographically verify the Canva JWT on every request before proxying the media to X.

### API & Communication Patterns

- **Decision:** Standard REST API (`POST /api/publish`)
- **Rationale:** Simplest to build, zero setup overhead, and aligns perfectly with how Canva Apps SDK natively uses `fetch()`.

### Frontend Architecture

- **Decision:** React + `@canva/app-ui-kit`
- **Rationale:** Mandatory for all Canva Apps. State management will use standard React Hooks, localized to the specific Canva intent panels (Settings, Preview).

### Infrastructure & Deployment

- **Decision:** Vercel (Next.js Serverless API Routes)
- **Rationale:** Vercel provides the optimal environment for stateless Edge and Node.js serverless functions, guaranteeing automated scaling for the proxy without managing infrastructure.

### Decision Impact Analysis

**Implementation Sequence:**
1. Scaffold Canva Frontend App using `@canva/cli`.
2. Scaffold Next.js proxy on Vercel.
3. Establish the Canva JWT Verification middleware in Next.js.
4. Implement the `OutputMedia` pass-through from Canva to X API.

**Cross-Component Dependencies:**
The Next.js proxy depends cryptographically on the Canva JWT. The proxy middleware must handle dynamic JWKS (JSON Web Key Set) resolution automatically to prevent authentication failures if Canva rotates their keys.

## Implementation Patterns & Consistency Rules

### Naming Patterns

**API Naming Conventions:**
- Proxy endpoints inside Next.js use standard REST action names (e.g., `/api/publish/tweet`, `/api/publish/media`).

**Code Naming Conventions:**
- React Components use `PascalCase` (e.g., `AccountSelector.tsx`).
- React Hooks use camelCase `usePascalCase` (e.g., `useXAccount.ts`).
- Serverless Route handlers strictly map to their domain actions.

### Structure Patterns

**Project Organization:**
- Feature-based grouping: `src/features/Settings/` and `src/features/Preview/`.
- Shared UI components in `src/shared/components/`.
- Shared Canva SDK hooks in `src/shared/hooks/`.

**File Structure Patterns:**
- Logic explicitly isolating X API calls into an external module `lib/services/x-api.ts` vs the Next.js Route interface `app/api/publish/route.ts`.

### Format Patterns

**API Response Formats:**
All Next.js API route responses MUST follow the strict JSON wrapper:
`{ success: boolean, data?: Payload, error?: { code: string, message: string } }`

### Enforcement Guidelines

**All AI Agents MUST:**
- Never leak state between `renderSettingsUi` and `renderPreviewUi` since they render independently.
- Always unwrap the strictly typed API Response Format before evaluating HTTP responses in the frontend.
- Mock all `lib/services/x-api.ts` functions during testing; do not perform live Next.js boots for unit testing.

## Project Structure & Boundaries

### Complete Project Directory Structure

```text
canva-x-publisher/
├── canva-frontend/                 # Canva App SDK (React)
│   ├── package.json
│   ├── tsconfig.json
│   ├── .env.example
│   ├── src/
│   │   ├── index.tsx               # App entry point (prepareContentPublisher)
│   │   ├── features/
│   │   │   ├── Settings/           # Caption editor, Account selector
│   │   │   │   ├── SettingsUI.tsx
│   │   │   │   └── useSettings.ts
│   │   │   └── Preview/            # WYSIWYG X Feed simulation
│   │   │       ├── PreviewUI.tsx
│   │   │       ├── XCard.tsx
│   │   │       └── usePreview.ts
│   │   ├── shared/
│   │   │   ├── components/         # Shared UI Kit wrappers
│   │   │   ├── hooks/              # useCanvaAuth hook
│   │   │   └── utils/              # 5KB Payload pre-flight validators
│   │   └── types/                  # X API and Canva SDK interfaces
│   └── tests/
│       ├── features/
│       └── shared/
│
└── nextjs-proxy/                   # Stateless Serverless Backend
    ├── package.json
    ├── next.config.js
    ├── tsconfig.json
    ├── .env.local                  # Canva App Secret, X API Keys
    ├── src/
    │   ├── app/
    │   │   └── api/
    │   │       └── publish/
    │   │           └── route.ts    # Main upload proxy
    │   ├── lib/
    │   │   ├── canva/
    │   │   │   └── jwt.ts          # Canva JWKS Signature verification
    │   │   └── services/
    │   │       └── x-api/
    │   │           ├── client.ts   # X API Core Fetcher
    │   │           └── media.ts    # Chunked INIT/APPEND/FINALIZE logic
    │   └── types/
    └── tests/
        ├── api/                      # Integration tests for route
        └── services/                 # X API Unit tests isolated from Next.js
```

### Architectural Boundaries

**API Boundaries:**
- The Next.js `/api/publish` boundary acts as the strict demarcation between the Canva context (Frontend) and the X context (External API). It enforces strict payload parsing and Canva JWT cryptographic authentication before executing upstream requests to X.

**Component Boundaries:**
- `SettingsUI` and `PreviewUI` communicate exclusively via Canva's native `setContent` and `registerOnPublishConfigChange` callback methods. No global state management (like Redux or Context) wraps the two separate React roots, matching the exact runtime environment of the Canva side panel iframe.

**Service Boundaries:**
- `lib/services/x-api/` has absolute zero dependencies on Next.js Request or Response objects. It only accepts standard TypeScript primitives (`FileBuffer`, `Tokens`, strings), ensuring the proxy logic remains completely pure, independently testable, and reusable if we ever move off Next.js.

### Requirements to Structure Mapping

**Epic/Feature Mapping:**
- **FR4: WYSIWYG X Preview:** `canva-frontend/src/features/Preview/XCard.tsx`
- **FR5: X Media Pre-flight:** `canva-frontend/src/shared/utils/validators.ts`
- **FR18/FR19: Chunked Media Upload:** `nextjs-proxy/src/lib/services/x-api/media.ts`
- **NFR10: JWT Verification:** `nextjs-proxy/src/lib/canva/jwt.ts`

## Architecture Validation Results

### Coherence Validation ✅

**Decision Compatibility:**
The pivot to a "Zero Database" architecture ensures perfect compatibility between the stateless Next.js API Routes and Canva's native OAuth capability. 

**Pattern Consistency:**
The strict API Response Wrapper (`{ success, data, error }`) standardizes how complex X API rejections are routed back and displayed in the Canva UI, ensuring robust error handling.

**Structure Alignment:**
The dual-repo structure (`canva-frontend` and `nextjs-proxy`) perfectly separates the Canva SDK context from the secure backend execution environment, enforcing boundaries naturally.

### Requirements Coverage Validation ✅

**Functional Requirements Coverage:**
All synchronous FRs from the PRD (Post Now, OAuth Connect, Feed Preview) map directly to the Settings/Preview components and the `POST /api/publish` proxy route. 

**Non-Functional Requirements Coverage:**
- **NFR6 (Security):** Achieved natively by Canva's encrypted token storage.
- **NFR10 (JWT Verify):** Explicitly mapped to `nextjs-proxy/src/lib/canva/jwt.ts`.
- **NFR12 (Scalability):** Vercel Serverless guarantees automated horizontal scaling for the 50,000 MAU target without infra maintenance.

### Architecture Completeness Checklist

**✅ Requirements Analysis**
- [x] Project context thoroughly analyzed
- [x] Technical constraints identified

**✅ Architectural Decisions**
- [x] Technology stack fully specified
- [x] Database requirements eliminated (Stateless Pivot)

**✅ Implementation Patterns**
- [x] Naming and structure patterns defined
- [x] API communication formatting established

**✅ Project Structure**
- [x] Complete directory structure mapped to Epics

### Architecture Readiness Assessment

**Overall Status:** READY FOR IMPLEMENTATION

**Confidence Level:** HIGH. By dropping the custom backend database and server-side scheduling queues, we eliminated the highest-risk engineering components. The remaining proxy architecture is incredibly standard, fast, and secure.

### Implementation Handoff

**First Implementation Priority:**
```bash
# Frontend
npx @canva/cli create --template "content_publisher" my-canva-app

# Backend
npx create-next-app@latest my-stateless-proxy --typescript --eslint --no-src-dir --no-tailwind --app
```
