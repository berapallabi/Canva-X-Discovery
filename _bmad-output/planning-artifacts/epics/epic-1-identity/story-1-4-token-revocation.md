# Story 1.4: Strict Token Revocation & Disconnect

### 1. Core User Story Statement
**As a** privacy-conscious Creator,
**I want to** explicitly disconnect an X account directly from the app interface,
**So that** my data is instantly purged from Canva's vault and the app loses all publishing access permanently.

### 2. Acceptance Criteria

*   **AC1: Initiating Disconnect (Happy Path)**
    *   **Given** I have at least one X account connected
    *   **When** I open the Account Profile dropdown menu
    *   **Then** I MUST see a clear "Disconnect" button or trashcan icon next to every distinct account in the list.
    *   **And** it should be colored red or styled destructively to indicate permanent action.

*   **AC2: Protective Confirmation Block (Error Handling)**
    *   **Given** I click the "Disconnect" button
    *   **When** the event fires
    *   **Then** the app MUST not instantly delete the account.
    *   **And** I MUST be presented with a native Canva Modal asking: "Are you sure you want to disconnect [X Display Name]? You will need to log in again to publish." with "Cancel" and "Disconnect" options.

*   **AC3: Executing the Purge (Data Handling)**
    *   **Given** I am looking at the confirmation modal
    *   **When** I explicitly click the confirmation "Disconnect" CTA
    *   **Then** the UI must show a loading spinner.
    *   **And** the backend MUST explicitly call the Next.js `/api/auth/revoke` endpoint to destroy the OAuth link on X's servers.
    *   **And** the app MUST use Canva's native `auth` SDK to permanently delete the cached tokens.

*   **AC4: Returning to Zero-State (UI Behavior)**
    *   **Given** I had only one X account connected total
    *   **When** the disconnect API call succeeds
    *   **Then** the Active Session header MUST immediately unmount.
    *   **And** the app MUST seamlessly return me to the Zero-State UI from Story 1.1 (showing the large blue "Connect to X" button) without crashing.

*   **AC5: Remaining in Active State (Edge Case)**
    *   **Given** I had multiple (e.g., three) X accounts connected
    *   **When** I disconnect exactly one of them
    *   **Then** the dropdown MUST close and the disconnected account MUST vanish from the list.
    *   **And** the app MUST seamlessly switch the Active Context to one of the remaining connected accounts, keeping me in the Authenticated UI.

*   **AC6: Handling External Revocations (Integration / Negative Scenario)**
    *   **Given** I am logged into the Canva app, but I went to X.com natively in another tab and manually clicked "Revoke Access" in Twitter's security settings
    *   **When** I return to Canva and try to interact with the Settings UI
    *   **Then** the backend proxy MUST catch the resulting `401 Unauthorized` token rejection from X natively.
    *   **And** the Next.js proxy MUST command the Canva frontend to trigger this exact Story 1.4 Disconnect logic automatically to clear the "dead" cache and return me to Zero-State.

### 3. Business Context / Objective
GDPR and CCPA Right to Be Forgotten. Providing an immediate, user-facing native destructive action proves to users that they have absolute sovereignty over their social media security boundaries without tracking down hidden menus.

### 4. In scope
- The Disconnect CTA button in the dropdown.
- The Confirmation Modal.
- The Zero-State fallback mapping.
- Next.js revoke API logic.

### 5. Out of Scope
- Logging out the user's primary Canva account identity (only targets the specific X identity).

### 6. UI/UX References
- Standard `Modal` from Canva App UI Kit.
- Destructive Red Action Buttons.

### 7. Dependencies
- Dependent on Story 1.1 (Fallback UI) and Story 1.3 (Dropdown rendering).

### 8. Technical Notes
OAuth token revocation requires hitting the specific `/2/oauth2/revoke` endpoint on X natively from the proxy backend. Standard credential wipes inside Canva's local storage are insufficient if the session isn't formally destroyed at the provider level.

### 9. Testing scenarios
- **Test:** Click Disconnect, click Cancel. **Expectation:** Modal closes, account remains active natively securely.
- **Test:** Click Disconnect, click Confirm. **Expectation:** Returns to blue "Connect" screen.
- **Test:** Revoke token manually on X.com dashboard, then click around the Canva app. **Expectation:** Forces organic logout smoothly without crashing the React root natively seamlessly.

### 10. Test Data
- Standard valid sandbox token payloads.