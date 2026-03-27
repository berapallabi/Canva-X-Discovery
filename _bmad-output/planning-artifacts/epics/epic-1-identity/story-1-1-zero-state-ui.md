# Story 1.1: Build Zero-State Deferred Auth UI

As an **unauthenticated User**,
I want **to interact with the Settings UI to draft my post and preview media before logging in**,
So that **I can evaluate the value of the integration without hitting an immediate OAuth hard-wall**.

**Acceptance Criteria:**

**Given** the user launches the app for the very first time without any stored X tokens,
**When** the main Side Panel (`index.tsx`) loads,
**Then** the UI MUST render the "Unauthenticated State" allowing full interaction with the Caption text area and Media selections.
**And** the "Publish" button MUST be locked, replaced by a persistent "Connect to X" CTA button located directly above the lock.
**And** the UI MUST explicitly list the exact requested scopes (`tweet.read`, `tweet.write`, `users.read`, `offline.access`) immediately adjacent to the Connect CTA.
