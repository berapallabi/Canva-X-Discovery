# Story 2.1: Caption Editor & Character Limits

### 1. Core User Story Statement
**As an** authenticated Canva Creator,
**I want to** type my post caption and have real-time feedback on character length,
**So that** my text is optimized for X's platform-native limitations.

### 2. Acceptance Criteria

*   **AC1: Entering Text (Happy Path)**
    *   **Given** I am in the Settings UI
    *   **When** I locate the Caption Editor text box
    *   **Then** I MUST be able to freely type text into the multi-line input field.
    *   **And** a live visual character counter MUST render clearly below it.

*   **AC2: Standard Character Limits (Validation / Negative Scenario)**
    *   **Given** I am typing a caption
    *   **When** I exceed exactly 250 characters
    *   **Then** the character counter MUST turn distinctive red.
    *   **And** the "Publish to X" button MUST physically disable to prevent API failure.

*   **AC3: Adaptive Hashtag Detection (UI Behavior)**
    *   **Given** I am typing in the Caption Editor
    *   **When** I type a string starting with `#` followed by alphanumeric characters
    *   **Then** the UI MUST physically highlight this substring differently from the standard text (e.g., brand color blue).
    *   **And** the character counter MUST correctly include the `#` symbol in the final count.

### 3. Business Context / Objective
Providing accurate limitations (strict 250 character hard-caps per the revised workflow) ensures our users don't waste time on API rejections.

### 4. In scope
- Multi-line Canva App UI Kit `TextInput`.
- Live character counting logic (handling unicode/emoji weights properly).

### 5. Out of Scope
- Premium or expanded character overrides (completely blocked/parked).
- Threaded tweets/multi-post chaining (Parked in MVP).

### 6. UI/UX References
- Standard `TextArea` components from the Canva App UI Kit.

### 7. Dependencies
- Dependent on Epic 1 Authentication state to ensure context availability.

### 8. Technical Notes
Standard `.length` char counting fails on Emojis because Emojis count as 2+ characters in UTF-16 strings. Use standard `Array.from(string).length` or the official `@twitter/twitter-text` NPM package for weighted counting. Calculate against 250 explicitly.

### 9. Testing scenarios
- **Test:** Type 251 characters. **Expectation:** Confirm Publish button locks and counter turns red.

### 10. Test Data
- Test strings including multi-byte characters and complex emojis.
