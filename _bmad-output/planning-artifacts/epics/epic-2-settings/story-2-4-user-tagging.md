# Story 2.4: Social Connectivity (User Tagging)

### 1. Core User Story Statement
**As a** Canva Creator,
**I want to** mention/tag other users in my post,
**So that** they are notified and my content gains higher visibility through their social graphs.

### 2. Acceptance Criteria

*   **AC1: Tagging Input (Happy Path)**
    *   **Given** I am in the settings panel
    *   **When** I locate the "Tag People" input field
    *   **Then** I MUST be able to type X handles (prefixed with `@`).
    *   **And** the UI MUST provide a standard text-based tagging list.

*   **AC2: API v2 Tagging Limitations (UI Behavior / Constraint)**
    *   **Given** X API v2 physically restricts 3rd-party apps from performing "Photo Tagging" (marking usernames on specific parts of a image)
    *   **When** the user attempts to tag people
    *   **Then** the UI MUST clearly display an information tooltip or hint: "Direct photo tagging is restricted by X API. These handles will be added as @mentions in your caption."
    *   **And** the app MUST automatically correctly append these `@mentions` to the end of the caption payload.

### 3. Business Context / Objective
Social visibility. Tagging is the primary way to drive virality and collaborative engagement on X. 

### 4. In scope
- Tagging input UI block.
- Logic to append tags to the final caption string safely.

### 5. Out of Scope
- Real-time user autocomplete search (which would require a highly-rate-limited and expensive lookup on every keystroke).

### 6. UI/UX References
- Simple `TextInput` with chip-based entry logic.

### 7. Dependencies
- Dependent on **Story 2.1** (Caption Editor) as the final destination for the tags.

### 8. Technical Notes
Ensure rigorous validation with regex to ensure handles only contain Alphanumeric and `_` characters.

### 9. Testing scenarios
- **Test:** Enter `@canva`. **Expectation:** App confirms it will be appended to the tweet.

### 10. Test Data
- Valid and invalid handle formats.
