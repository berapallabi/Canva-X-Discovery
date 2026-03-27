# Story 1.1: Build Zero-State Onboarding UI

### 1. Core User Story Statement
**As a** Canva Creator launching the app for the first time,
**I want to** clearly see the app's interface, its purpose, and exactly what permissions it requires before I log in,
**So that** I feel secure connecting my X (Twitter) account without hitting a forced login wall immediately.

### 2. Acceptance Criteria

*   **AC1: Initial App Launch Interface (Happy Path)**
    *   **Given** I have never connected my X account to this Canva App
    *   **When** I open the Canva-to-X app in the side panel
    *   **Then** I should immediately see the app's main layout load within 2 seconds (NFR1).
    *   **And** I should see a locked/disabled primary "Publish to X" button anchored at the bottom of the panel.
    *   **And** I should see a prominent blue "Connect to X" button rendered directly above the disabled publish button.
    *   **And** I should see placeholder space where my profile information will eventually go.

*   **AC2: Transparent Scopes & Permissions (UI Behavior)**
    *   **Given** I am viewing the unauthenticated "Connect to X" screen
    *   **When** I read the text below or beside the "Connect" button
    *   **Then** I MUST see a clear, human-readable list of specific permissions the app is asking for (e.g., "This app can: Read your profile, Post tweets for you. It cannot: Read your DMs").
    
*   **AC3: Clicking Connect (State Change & Negative Scenario)**
    *   **Given** I am on the unauthenticated screen
    *   **When** I click "Connect to X"
    *   **Then** the button text should change to "Connecting..." or display a loading spinner so I know the app is working.
    *   **And** if my internet connection drops the exact moment I click it, the button must revert to "Connect to X" and show a small red error stating "Network connection failed. Please try again."

*   **AC4: Seamless Session Expiry (Edge Case / Error Handling)**
    *   **Given** I was previously logged in, but my X password changed or my session naturally expired
    *   **When** I open the Canva editor and launch the app
    *   **Then** I MUST NOT see a harsh full-screen red error or a generic "401 Unauthorized" crash message.
    *   **And** the app must quietly return me to this Zero-State UI with the "Connect to X" button ready for me to log in again.

*   **AC5: Epic 2 Composition Interaction (Dependency Mapping)**
    *   **Given** the "Connect to X" button wrapper is active at the bottom of the panel
    *   **When** the actual Post Composition fields (Caption Editor, Page Selector) are implemented later in **Epic 2**
    *   **Then** this Zero-State UI MUST permit those fields to render *above* it.
    *   **And** I MUST be able to fully type a caption and select pages without being blocked by the "Connect" overlay, ensuring true Deferred Authentication.

### 3. Business Context / Objective
Immediate forced logins (Auth Walls) have a 60% abandonment rate. By building a "Zero-State" UI that allows the user to see the app's interface and (eventually) draft their post before logging in, we increase feature adoption and user trust.

### 4. In scope
- The visual layout of the unauthenticated app panel.
- The "Connect to X" button and its loading states.
- The visual permissions list.
- The disabled "Publish" button wrapper.

### 5. Out of Scope
- Actually executing the pop-up window or saving the tokens (This is Story 1.2).
- The actual text inputs for drafting the post (These belong to Epic 2: Settings Panel).

### 6. UI/UX References
- Standard Canva App UI Kit `Button` component (Solid Blue for primary actions).
- Standard Canva App UI Kit `Text` component for the small permissions list (Subdued grey text).

### 7. Dependencies
- Must be built first to establish the `<AppContainer>` before any Settings (Epic 2) or Previews (Epic 3) can be mounted.

### 8. Technical Notes
The React component should use a simple `isAuthenticated` boolean state. If false, it renders this `ZeroStateFooter` component over the publish area. 

### 9. Testing scenarios
- **Test:** Open app with cleared browser cache. **Expectation:** See "Connect to X" button and disabled publish button.
- **Test:** Click "Connect" while offline. **Expectation:** Button spins, then fails gracefully with a human-readable error.
- **Test:** Ensure the load time to see this initial screen is under the 2-second NFR limit.

### 10. Test Data
- None required for this pure UI state.