# Story 1.4: Strict Token Revocation & Disconnect

As a **Privacy-Conscious User**,
I want **to explicitly disconnect my X account from the Settings dropdown**,
So that **my OAuth tokens are physically purged from Canva's secure storage**.

**Acceptance Criteria:**

**Given** the user has opened the Account Dropdown,
**When** they click the distinct "Disconnect" action associated with an account,
**Then** a protective "Are you sure?" confirmation dialog MUST render.
**And When** the user confirms the action,
**Then** the app MUST execute a `DELETE` request to purge the associated X OAuth tokens from Canva's secure storage API.
**And** if no other authenticated accounts remain, the UI MUST return the user entirely back to the "Unauthenticated Zero-State".
