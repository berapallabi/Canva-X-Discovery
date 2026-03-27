# Story 2.6: Post Privacy (Reply Controls)

### 1. Core User Story Statement
**As a** Canva Creator,
**I want to** restrict who can reply to my post before I publish it,
**So that** I can protect myself or my brand from unwanted engagement or quote-tweet harassment.

### 2. Acceptance Criteria

*   **AC1: Reply Controls Selection (Happy Path)**
    *   **Given** I am in the settings panel
    *   **When** I locate the "Who can reply?" dropdown menu
    *   **Then** I MUST see three explicit options precisely mirroring X native platform:
        1. `Everyone` (Default)
        2. `Accounts you follow`
        3. `Only accounts you mention`

*   **AC2: State Mapping (Data Handling)**
    *   **Given** I select "Accounts you follow"
    *   **When** I prepare the final payload for Epic 4
    *   **Then** the selection MUST be mapped to the exact X API v2 enum string: `following`.
    *   **And** this must be stored in the `reply_settings` field.

*   **AC3: Resetting Defaults (Interaction)**
    *   **Given** I have changed the setting to "Only accounts you mention"
    *   **When** I refresh the app or click a "Reset" button
    *   **Then** it MUST revert to "Everyone" to prevent accidental privacy lockouts on future posts.

### 3. Business Context / Objective
Safety and Brand Management. Social media is increasingly toxic; high-profile users require these controls to maintain sanity while publishing.

### 4. In scope
- Canva App UI Kit `Select` or `RadioGroup` component.
- Mapping state to API enums.

### 5. Out of Scope
- Blocking users (This is an account management feature outside the scope of a publishing app).

### 6. UI/UX References
- X standard reply control icons and text.

### 7. Dependencies
- Dependent on Epic 1 Auth.

### 8. Technical Notes
Map the local UI labels directly to `everyone`, `following`, and `mentionedUsers` as per X API v2 documentation.

### 9. Testing scenarios
- **Test:** Change to "Mentioned Users". **Expectation:** Final JSON payload contains `"reply_settings": "mentionedUsers"`.

### 10. Test Data
- None required.
