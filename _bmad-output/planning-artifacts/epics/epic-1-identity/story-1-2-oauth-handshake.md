# Story 1.2: Execute Canva OAuth Handshake

### 1. Core User Story Statement
**As a** Canva Creator viewing the zero-state screen,
**I want to** click "Connect to X" and log into my Twitter account securely through an official popup,
**So that** my profile is linked to the app and I can begin publishing.

### 2. Acceptance Criteria

*   **AC1: Launching the Secure Popup (Happy Path)**
    *   **Given** I am on the unauthenticated app screen
    *   **When** I click the "Connect to X" button
    *   **Then** an official Canva-wrapped browser popup MUST immediately open displaying the official X.com authentication page.
    *   **And** the popup MUST NOT be a generic webview embedded directly inside the panel (for security reasons).

*   **AC2: Approving the Connection (Happy Path & State Change)**
    *   **Given** I am looking at the official X.com popup
    *   **When** I enter my credentials and click "Authorize App"
    *   **Then** the popup window must automatically close.
    *   **And** the Canva side panel must immediately instantly update to the "Authenticated State".
    *   **And** I must see an "Account Profile Block" materialize at the top of the panel displaying my actual X Display Name and `@handle`.

*   **AC3: Rejecting the Connection (Negative Scenario / Error Handling)**
    *   **Given** the X.com popup is open
    *   **When** I click "Cancel", "Deny", or manually close the popup window via the 'X' icon
    *   **Then** the Canva side panel must gracefully revert the "Connecting..." button back to the standard "Connect to X" state.
    *   **And** I MUST NOT be locked out of the app or shown a white crash screen.

*   **AC4: High-Latency Connection (Edge Case)**
    *   **Given** I have clicked "Connect to X" and authorized the app, but my internet is extremely slow
    *   **When** the popup closes and the Canva backend is exchanging the secure tokens
    *   **Then** the Canva side-panel must continue showing a "Connecting..." loading state until the backend confirms the token is securely locked in the vault, preventing me from clicking buttons out of order.

*   **AC5: Un-hijackable Authorization (Validation Rules)**
    *   **Given** a malicious actor attempts to spoof the OAuth callback redirect link
    *   **When** the tokens arrive at the app's backend proxy
    *   **Then** the connection must silently fail if the security `state` parameter does not exactly match the one generated when I opened the popup.

*   **AC6: Complete Session Persistence (Data Handling)**
    *   **Given** I have successfully connected my X account
    *   **When** I close my Canva browser tab completely, reboot my computer, and open the Canva design the next day
    *   **Then** I MUST still be logged in automatically without seeing the "Connect to X" screen.
    *   **And** my X tokens must be retrieved silently and securely in the background.

*   **AC7: Zero-Retention Protection (Data Handling)**
    *   **Given** my tokens are stored
    *   **When** the backend saves them
    *   **Then** they MUST be stored strictly in Canva's official secure infrastructure. No custom, hackable 3rd-party database can be used to hold my identity data.

*   **AC8: Draft Persistence Across Auth Boundary (Data Handling)**
    *   **Given** I have already drafted a post (Caption, Format, Selected Pages) in the Zero-State
    *   **When** I click "Connect to X" and successfully complete the OAuth handshake
    *   **Then** upon the panel refreshing to the "Authenticated State", ALL my drafted content MUST remain exactly as I left it.
    *   **And** the app MUST NOT clear the React state or reset the composition fields to empty.

### 3. Business Context / Objective
Trust and Security are paramount for platform applications. By utilizing the native Canva popup and vault infrastructure, we guarantee to the user that we are not stealing their X passwords or retaining their tokens on unsecured private servers. 

### 4. In scope
- The physical browser popup execution.
- Receiving the tokens.
- Updating the UI to show the user's `@handle`.

### 5. Out of Scope
- Actually publishing a tweet with the tokens (Epic 4).
- Switching between multiple different X accounts (Epic 1, Story 1.3).

### 6. UI/UX References
- Standard `Users/Me` data return layout: Display Name (Bold), @handle (grey subtext).
- X (Twitter) Avatar circular icon.

### 7. Dependencies
- Dependent on Story 1.1 being finished (so the Connect button exists).
- Requires Canva App backend APIs configured physically on Vercel.

### 8. Technical Notes
The transition from unauthenticated to authenticated happens seamlessly because the Canva `auth.requestAuthentication()` SDK method resolves a Promise directly in the frontend exactly when the popup finishes.

### 9. Testing scenarios
- **Test:** Click Connect, login correctly. **Expectation:** See profile name appear at top of panel instantly.
- **Test:** Click Connect, then abruptly close the popup. **Expectation:** Loading spinner stops, panel reverts to normal Connect state.
- **Test:** Refresh page after successful login. **Expectation:** Profile name loads instantly on boot.

### 10. Test Data
- One valid X test sandbox account.