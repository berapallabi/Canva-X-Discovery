# Story 2.4: User Tagging

### 1. Core User Story Statement
**As an** authenticated Canva Creator,
**I want to** explicitly tag up to 10 X accounts in my post,
**So that** I physically notify partners and friends about my published composition.

### 2. Acceptance Criteria

*   **AC1: Tagging Interface (Happy Path)**
    *   **Given** I am looking at the Settings interface
    *   **When** I locate the "Tag People" field
    *   **Then** I MUST be able to type search queries or `@handles` organically.
    *   **And** the UI must accumulate these selected tags visually as encapsulated UI pills.

*   **AC2: Hard Native Overload Limits (Validation Check)**
    *   **Given** I am selecting tags
    *   **When** I hit exactly 10 user tags
    *   **Then** the UI MUST block the addition of an 11th tag.
    *   **And** It must render text clearly stating "Max 10 tags reached."

*   **AC3: Graceful Post-Level Degradation (API Constraints)**
    *   **Given** I have added 4 user tags to the UI
    *   **When** the overall payload compiles
    *   **Then** because the X API v2 restricts physical coordinates matching for visual media tagging, the system MUST gracefully string-append these `@handles` to the end of the post text natively, protecting them as post-level tags securely.

### 3. Business Context / Objective
To drive distribution, users need cross-account notifications. The limit of 10 matches the current scope limits smoothly and bypasses visual media tagging coordinate bugs by elevating them to post-level string blocks.

### 4. In scope
- Tagging Search UI Box.
- Accumulator pills tracking up to 10 targets.

### 5. Out of Scope
- Actually querying X API for user typeahead suggestions (which might be blocked anyway). Can default to basic string input.

### 6. UI/UX References
- Standard Canva `TextInput` with chip/pill multi-select patterns.

### 7. Technical Notes
If `users/by/username` typeahead is restricted, simply allow physical text strings and assume validation happens upon final POST.

### 8. Testing scenarios
- **Test:** Add 11 handles natively. **Expectation:** 11th is stopped.
