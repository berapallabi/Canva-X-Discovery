# Story 1.3: Multi-Account Context Switching

As a **Power User**,
I want **to connect multiple distinct X accounts and cycle between them via a dropdown menu**,
So that **I can manage different brand profiles securely within a single Canva context**.

**Acceptance Criteria:**

**Given** the user has successfully linked at least one X account,
**When** they navigate to the top of the Settings UI,
**Then** they MUST see an Account Profile Card displaying their X `@handle`, User Display Name, and Profile Avatar fetched from the X API `users/me` endpoint.
**And Given** the Account Profile Card is clicked,
**Then** it MUST expand into a dropdown Account Selector listing all currently authenticated X accounts + an "Add new account" button.
**And When** an alternate account is selected,
**Then** the active publishing context MUST instantly switch, and the Preview WYSIWYG simulator MUST immediately re-render with the newly selected account's Avatar and `@handle`.
