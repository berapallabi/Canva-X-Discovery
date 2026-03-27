# Story 1.3: Multi-Account Context Switching

### 1. Core User Story Statement
**As a** Social Media Manager or Agency User managing multiple brands,
**I want to** securely connect multiple distinct X accounts and seamlessly swap between them via a native dropdown menu,
**So that** I don't have to constantly log out and log back in just to publish the same design to different clients.

### 2. Acceptance Criteria

*   **AC1: Entering Multi-Account Selection (Happy Path)**
    *   **Given** I am logged into at least one X account and viewing the Settings UI
    *   **When** I click my active Account Profile block at the top of the panel
    *   **Then** a dropdown menu MUST open.
    *   **And** it must list my currently active account with a checkmark.
    *   **And** it must display an explicitly separated "Add Another Account" button at the bottom of the list.

*   **AC2: Connecting a Secondary Account (State Change)**
    *   **Given** the account dropdown is open
    *   **When** I click "Add Another Account"
    *   **Then** the Canva OAuth popup must execute identical to Story 1.2.
    *   **And** upon successful connection, the dropdown must close.
    *   **And** the newly connected account MUST become the currently "Active" account automatically.

*   **AC3: Swapping Active Contexts (UI Behavior)**
    *   **Given** I have at least two X accounts connected to the app
    *   **When** I select the non-active account from the dropdown list
    *   **Then** the app MUST instantly update the top-level Profile block to reflect the new account's Display Name and Avatar.
    *   **And** if Epic 3 (WYSIWYG Preview) is implemented, the simulator must instantly re-render the @handle and profile picture to match the newly active account.

*   **AC4: Exceeding Maximum Account Limits (Validation/Edge Case)**
    *   **Given** I have reached the maximum allowed number of connected X accounts (e.g., 5 accounts based on NFR constraints)
    *   **When** I open the dropdown menu
    *   **Then** the "Add Another Account" button MUST be disabled or hidden.
    *   **And** a small tooltip or text must explain "Maximum of 5 accounts reached."

*   **AC5: Local Storage Key Handling (Data Handling)**
    *   **Given** I am swapping between accounts continuously
    *   **When** the app reads/writes tokens
    *   **Then** it MUST seamlessly map distinct X user IDs to distinct Canva token vault keys automatically natively, ensuring one account's publish action doesn't accidentally use another account's credential.

### 3. Business Context / Objective
To drive B2B and Agency adoption, Canva Apps must support friction-free multi-tenant posting. Forcing an agency user to revoke tokens and re-login every 5 minutes completely ruins the "instant publish" value proposition.

### 4. In scope
- The dropdown menu component overlaying the Settings layout.
- Logic to manage an array of authenticated profiles in React state.
- The "Add Another Account" action routing directly to Story 1.2's popup logic.

### 5. Out of Scope
- Actually publishing messages simultaneously to multiple accounts (Blocked by X API redundancy rules). The user must switch and publish one at a time.

### 6. UI/UX References
- Standard `Select` or custom Dropdown block native to Canva App UI Kit.
- Uses X Avatars (24x24px circular).

### 7. Dependencies
- Dependent on Story 1.2 for the actual OAuth logic.
- Epics 2 and 3 must listen to an `activeAccountId` context provider so they know which payload rules to simulate.

### 8. Technical Notes
Canva's `auth` SDK is a flat key/value store mapping a single Canva User ID. The Next.js backend MUST orchestrate an array of connected X accounts inside that vault safely, returning a sanitized list of profiles to the frontend.

### 9. Testing scenarios
- **Test:** Connect Account A, click Add Account, connect Account B. **Expectation:** Both appear in the dropdown.
- **Test:** Select Account A. **Expectation:** Preview updates instantly to A.
- **Test:** Reload Canva entirely. **Expectation:** Both accounts are still cached.

### 10. Test Data
- Two distinct X Sandbox developer accounts.