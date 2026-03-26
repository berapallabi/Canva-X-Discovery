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

## Technical Constraints & Payload Boundaries

- **Decision:** Strict 4MB File Chunking Protocol
- **Rationale:** While X technically allows up to 512MB for videos, our `Next.js` Serverless Proxy deployed on Vercel is strictly constrained by a hard 4.5MB request payload limit (Vercel standard). 
- **Enforcement:** The React frontend MUST slice all exported media (specifically MP4 videos) into exact `3.9MB` byte chunks. The `Next.js` proxy will act as a pure pipe, forwarding one chunk at a time via `POST /api/publish/chunk` using X's `APPEND` command, rather than risking a 413 Payload Too Large crash.

## Frontend Architecture

- **Decision:** React 18 + `@canva/app-ui-kit (v1.2+)`
- **Rationale:** Strict adherence to Canva App Requirements. No custom Tailwind or CSS-in-JS libraries are permitted. System state will utilize standard `useState` and `useContext` hooks to manage the complex Multi-Account dropdown and WYSIWYG Preview data shapes.

## Infrastructure & Vercel Limitations

- **Decision:** Vercel (Next.js 14+ Serverless API Routes)
- **Rationale:** Optimal zero-config Edge scaling. However, serverless functions have a 10-second timeout on Hobby tier and a 60-second limit on Pro. 
- **Mitigation:** Since uploading 512MB of video chunks to X could exceed 60 seconds, the frontend must orchestrate the loop, querying the proxy separately per chunk to reset the 60-second timeout clock gracefully, rather than sending a fire-and-forget monolithic request to the backend.

## Decision Impact Analysis

**Implementation Sequence:**
1. Scaffold Canva Frontend App using `@canva/cli`.
2. Scaffold Next.js proxy on Vercel.
3. Establish the Canva JWT Verification middleware in Next.js.
4. Implement the `OutputMedia` pass-through from Canva to X API.

**Cross-Component Dependencies:**
The Next.js proxy depends cryptographically on the Canva JWT. The proxy middleware must handle dynamic JWKS (JSON Web Key Set) resolution automatically to prevent authentication failures if Canva rotates their keys.
