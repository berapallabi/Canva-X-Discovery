# Project Context Analysis

## Requirements Overview

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

## Technical Constraints & Dependencies

- **Canva App Review:** Frontend strictly constrained to React + `@canva/app-ui-kit`. No custom CSS that breaks light/dark mode.
- **Payload Limits:** `PublishRef` object is capped at 5KB. Strict frontend validation is required.
- **X API Limits:** Free tier is limited to 500,000 tweets/month.

## Cross-Cutting Concerns Identified

1. **Idempotency & Retry Logic:** Network failures between Backend ↔ X API require a robust retry mechanism with a global circuit breaker.
2. **Session Management:** Securing the connection via Canva JWT to lookup backend X OAuth tokens securely.
3. **Observability:** Granular logging required to debug across the Canva Export step, the Backend intake step, and the X API delivery step.
