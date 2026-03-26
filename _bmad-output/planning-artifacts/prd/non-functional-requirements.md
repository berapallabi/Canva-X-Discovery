# Non-Functional Requirements

## Performance

- **NFR1:** The Settings UI must load and be interactive within 2 seconds of opening the side panel.
- **NFR2:** The X feed preview must render within 3 seconds of panel open.
- **NFR3:** "Post Now" must complete — from user click to tweet live — within 8 seconds at P95.
- **NFR4:** The full publish pipeline must achieve P50 latency of < 60 seconds (aligned with North Star metric).
- **NFR5:** Preview must update within 1 second of caption change (real-time feedback expectation).

## Security

- **NFR6:** X OAuth access tokens and refresh tokens must be stored server-side only, encrypted at rest (AES-256 or equivalent). Never stored in browser, frontend state, or Canva app storage.
- **NFR7:** All communication between the Canva app frontend and the backend must occur over HTTPS/TLS 1.2+.
- **NFR8:** The OAuth pop-up must follow Canva's pop-up guidelines — no auth forms displayed inside the app iframe.
- **NFR9:** Token revocation must be immediate upon user-initiated disconnect or app removal.
- **NFR10:** The backend must cryptographically verify the Canva-signed JWT (`Authorization` header) on every custom API request to securely identify the Canva User ID before interacting with their X tokens.

## Scalability

- **NFR11:** Next.js serverless architecture scales automatically to handle traffic spikes.
- **NFR12:** The app backend must scale to 10x user growth (50,000 MAU) without architectural changes — achieved via explicitly stateless API routing.
- **NFR13:** X API quota must be managed at the per-user level, preventing any single user's volume from affecting other users' availability.

## Accessibility & Platform Compliance

- **NFR14:** The Settings UI and Preview UI must meet WCAG 2.1 AA accessibility standards (required by Canva's app submission guidelines).
- **NFR15:** The app must support both Canva light mode and dark mode without visual regressions.
- **NFR16:** The app must be internationalisation-ready — all strings externalised, ready for translation (required for Canva Marketplace listing).
- **NFR17:** The app must be responsive across Canva's desktop (primary) and tablet (secondary) surfaces.

## Integration Reliability

- **NFR18:** The codebase must remain thoroughly stateless ensuring idempotent API deliveries.
- **NFR19:** Video media upload must use X's chunked upload endpoint (`/2/media/upload` with `INIT`/`APPEND`/`FINALIZE` commands).
- **NFR21:** The app must handle OAuth token expiry gracefully using Canva's getAccessToken method.
