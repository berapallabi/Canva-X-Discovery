# Core Architectural Decisions

## Decision Priority Analysis

**Critical Decisions (Block Implementation):**
- Dropped Custom Backend Database in favor of Canva's native OAuth storage.
- Standard REST API for cross-service communication.

**Deferred Decisions (Post-MVP):**
- Scheduling capabilities and Batch posting (which would require stateful database infrastructure).

## Data Architecture

- **Decision:** Zero Database Architecture (Stateless)
- **Rationale:** Rely entirely on Canva's native `auth` capability to securely store and refresh the X OAuth tokens. This eliminates the need for a PostgreSQL instance, lowering maintenance to near-zero.

## Authentication & Security

- **Decision:** Canva SDK `auth.initOauth()` + Canva JWT Verification
- **Rationale:** Offloading the OAuth flow to Canva ensures we don't handle token rotation. Our Next.js proxy will cryptographically verify the Canva JWT on every request before proxying the media to X.

## API & Communication Patterns

- **Decision:** Standard REST API (`POST /api/publish`)
- **Rationale:** Simplest to build, zero setup overhead, and aligns perfectly with how Canva Apps SDK natively uses `fetch()`.

## Frontend Architecture

- **Decision:** React + `@canva/app-ui-kit`
- **Rationale:** Mandatory for all Canva Apps. State management will use standard React Hooks, localized to the specific Canva intent panels (Settings, Preview).

## Infrastructure & Deployment

- **Decision:** Vercel (Next.js Serverless API Routes)
- **Rationale:** Vercel provides the optimal environment for stateless Edge and Node.js serverless functions, guaranteeing automated scaling for the proxy without managing infrastructure.

## Decision Impact Analysis

**Implementation Sequence:**
1. Scaffold Canva Frontend App using `@canva/cli`.
2. Scaffold Next.js proxy on Vercel.
3. Establish the Canva JWT Verification middleware in Next.js.
4. Implement the `OutputMedia` pass-through from Canva to X API.

**Cross-Component Dependencies:**
The Next.js proxy depends cryptographically on the Canva JWT. The proxy middleware must handle dynamic JWKS (JSON Web Key Set) resolution automatically to prevent authentication failures if Canva rotates their keys.
