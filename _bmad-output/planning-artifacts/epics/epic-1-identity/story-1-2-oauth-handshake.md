# Story 1.2: Implement Canva OAuth Token Exchange

As a **User**,
I want **to click "Connect to X" to securely exchange tokens without leaving my active Canva session**,
So that **my account is linked perfectly to the proxy backend**.

**Acceptance Criteria:**

**Given** the user is viewing the Zero-State UI,
**When** they click "Connect to X",
**Then** the frontend MUST trigger Canva's native `auth.requestAuthentication()` SDK method.
**And** a secure OAuth 2.0 popup MUST launch to X without navigating the user away from the active Canva Editor iframe.
**And** upon successful token exchange, the backend MUST securely store the X Refresh tokens using Canva's native Auth Storage APIs.
**And** the UI MUST seamlessly transition from "Unauthenticated" to "Authenticated" state without requiring a hard refresh of the panel.
