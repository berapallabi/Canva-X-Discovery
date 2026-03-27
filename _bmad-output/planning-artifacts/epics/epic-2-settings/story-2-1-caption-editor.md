# Story 2.1: Caption Editor & Output Formats

### 1. Core User Story Statement
**As an** authenticated Canva Creator,
**I want to** type my post caption and select whether I am publishing an Image, Video, or GIF,
**So that** my core payload is formatted correctly for X's validation limits.

### 2. Acceptance Criteria

*   **AC1: Entering Text (Happy Path)**
    *   **Given** I am in the Settings UI
    *   **When** I locate the Caption Editor text box
    *   **Then** I MUST be able to freely type text into the multi-line input field.
    *   **And** a live visual character counter MUST render clearly below it.

*   **AC2: Standard Character Limits (Validation / Negative Scenario)**
    *   **Given** I am typing a long caption
    *   **When** I exceed 280 characters
    *   **Then** the character counter MUST turn distinctive red.
    *   **And** the "Publish to X" button MUST physically disable to prevent API failure.

*   **AC3: Premium Character Override (Edge Case / UI Behavior)**
    *   **Given** I am an X Premium subscriber (allowing up to 25,000 characters)
    *   **When** I exceed the 280-character limit and the publish button locks
    *   **Then** the UI MUST display a clear checkbox nearby stating: "I have an X Premium account (Unlocks 25,000 limit)."
    *   **And** when I explicitly check this box, the character limit validation ceiling shifts to 25,000, and the "Publish" button unlocks.

*   **AC4: Output Format Selection (Data Handling)**
    *   **Given** the Caption box is filled out
    *   **When** I locate the "Export As" dropdown element
    *   **Then** I MUST explicitly select between `Static Image`, `Video (MP4)`, or `Animated GIF`.
    *   **And** this selection MUST mutate the internal state so the Preview simulator knows exactly how to render the media payload.

### 3. Business Context / Objective
Providing accurate, platform-native limitations (like 280 character hard-caps) ensures our users don't waste time on long background render processes just to have the X API reject it at the finish line.

### 4. In scope
- Multi-line Canva App UI Kit `TextInput`.
- Live character counting logic (handling unicode/emoji weights properly).
- The Premium Override checkbox.
- Output Format selection dropdown.

### 5. Out of Scope
- Uploading the actual media payload (Epic 4).
- The visual Preview simulation logic (Epic 3).

### 6. UI/UX References
- Standard `TextArea` and `Checkbox` components from the Canva App UI Kit.

### 7. Dependencies
- Dependent on Epic 1 Authentication state to ensure context availability.

### 8. Technical Notes
Standard `.length` char counting fails on Emojis because Emojis count as 2+ characters in UTF-16 strings. Use standard `Array.from(string).length` or the official `@twitter/twitter-text` NPM package for weighted counting.

### 9. Testing scenarios
- **Test:** Type 281 characters. **Expectation:** Confirm Publish button locks.
- **Test:** Click X Premium Checkbox. **Expectation:** Confirm button unlocks.

### 10. Test Data
- Test strings including multi-byte characters and complex emojis.
