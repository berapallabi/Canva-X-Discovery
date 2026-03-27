# Story 3.1: WYSIWYG Simulator Layout & Text Parsing

### 1. Core User Story Statement
**As a** Canva Creator designing a post,
**I want to** see an exact, real-time simulation of how my Tweet will look on the X Feed natively,
**So that** I have absolute confidence that my text won't truncate strangely or look unprofessional before I actually publish it.

### 2. Acceptance Criteria

*   **AC1: Simulator Header Instantiation (Happy Path)**
    *   **Given** I am looking at the Preview container element
    *   **When** I am in an Authenticated state (from Epic 1)
    *   **Then** the top of the preview MUST dynamically render my exact X Avatar, my Display Name in bold, my `@handle` in grey, and a mocked time string (e.g., "· 2h").
    *   **And** it must explicitly match X's system font stacks (e.g., `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto...`).

*   **AC2: Live Caption Rendering & Text Truncation (UI Behavior)**
    *   **Given** I am typing in the Settings UI Caption box (from Epic 2)
    *   **When** I type text into the box
    *   **Then** the Preview text MUST update instantly via real-time React state binding.
    *   **And** if my text exceeds standard X height limits, the simulator MUST NOT grow infinitely. It MUST accurately simulate the native "Show more" truncation cut-off logic used by X feeds.

*   **AC3: Rich Text Highlighting (Integration Behavior)**
    *   **Given** I type a hashtag (`#design`), an @mention (`@canva`), or a URL (`https://canva.com`)
    *   **When** the text is rendered in the Preview
    *   **Then** those specific substrings MUST be highlighted in standard X brand blue (`#1D9BF0`).
    *   **And** this must happen organically via regex text parsing inside the React component.

*   **AC4: Themed Preview Toggles (User Interaction / Accessibility)**
    *   **Given** I want to see how my design looks to followers who prefer dark mode
    *   **When** I click the small toggle icons above the preview box
    *   **Then** I MUST be able to immediately swap the Preview background to `Default` (White), `Dim` (Dark Blue/Grey), or `Lights Out` (Pure Black).
    *   **And** the text colors must invert appropriately to match X's native contrast algorithms.

### 3. Business Context / Objective
To drive premium engagement, users need a "What You See Is What You Get" simulator that mirrors the exact DOM layout of native X, so they can optimize their designs confidently.

### 4. In scope
- React `<TwitterCard>` component container mimicking the DOM structure.
- Real-time `onChange` listeners syncing the Epic 2 text inputs cleanly.

### 5. Out of Scope
- Simulating threaded replies (Only the primary parent tweet is previewed).

### 6. UI/UX References
- Native X Web Feed HTML/CSS structure.

### 7. Dependencies
- Dependent on Story 1.1 for authentication data (user avatar).
- Dependent on Story 2.1 for the active caption string.

### 8. Technical Notes
Do not embed a real Twitter API embed iframe, as that only supports historical, already-published tweets. This simulator must be built entirely from scratch using Canva App UI Kit primitive elements (`Box`, `Text`) styled to match X's CSS.

### 9. Testing scenarios
- **Test:** Type `#helloworld` into settings. **Expectation:** Instantly see it turn blue in the Preview.
- **Test:** Toggle Dark Mode. **Expectation:** Preview background turns #000000 and the text turns white.

### 10. Test Data
- None required.
