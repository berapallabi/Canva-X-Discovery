# Story 2.3: Advanced Settings (Reply Controls & Flags)

### 1. Core User Story Statement
**As an** authenticated Canva Creator drafting a post,
**I want to** configure advanced X features like who can reply to me and add Alt-Text to my media,
**So that** my post is accessible and maintains my preferred engagement privacy.

### 2. Acceptance Criteria

*   **AC1: Reply Controls Selection (Happy Path)**
    *   **Given** I am in the Settings UI
    *   **When** I locate the "Who can reply?" dropdown menu
    *   **Then** I MUST see three explicit options precisely mirroring X: `Everyone` (Default), `Accounts you follow`, and `Only accounts you mention`.
    *   **And** selecting one MUST bind the corresponding X API v2 Enum string (`everyone`, `following`, `mentionedUsers`) to the final payload.

*   **AC2: Alt-Text Addition (UI Behavior)**
    *   **Given** I have selected at least 1 page of media
    *   **When** I scroll to the "Accessibility" section
    *   **Then** I MUST see a distinct text-input box for *each* selected page.
    *   **And** each box MUST accept up to 1000 characters.

*   **AC3: Alt-Text Validation (Validation Rules)**
    *   **Given** I am typing Alt-Text for an image
    *   **When** I exceed 1000 characters
    *   **Then** the UI MUST block further input and display a validation error.

*   **AC4: Sensitive Content Mocking (Edge Case / API Constraint)**
    *   **Given** I want to flag my post as containing sensitive media
    *   **When** I check the "Flag as Sensitive" box
    *   **Then** the UI MUST explicitly include a subtext warning: "Preview only. X API blocks 3rd-party sensitivity flags. Your account's default media settings will apply."
    *   **And** checking the box MUST trigger the frosted-glass CSS blur inside the Preview Simulator (Epic 3), but MUST NOT attempt to pass the blocked boolean to the Next.js proxy.

### 3. Business Context / Objective
Parity with native platform execution. Without Alt-Text, we fail B2B brand compliance (WCAG standards). Without Reply Controls, high-profile creators risk unwanted engagement.

### 4. In scope
- Reply Controls selection dropdown.
- Multi-instance Alt-Text text areas dynamically mapped to the `selectedPages` array.
- The UX stub for Sensitive Flags.

### 5. Out of Scope
- Actually executing the `media/metadata/create` API logic (handled in the Epic 4 pipeline).

### 6. UI/UX References
- Standard `Select` and `TextInput` native components.

### 7. Dependencies
- Dependent on Story 2.2 (Media Selector) to determine how many Alt-Text boxes are required.

### 8. Technical Notes
Alt-text must be passed in the `PublishRef` JSON object as a mapping of page identifiers to their respective 1000-character strings.

### 9. Testing scenarios
- **Test:** Type 1001 characters in Alt-text. **Expectation:** UI prevents overflow input.
- **Test:** Select "Following" reply control. **Expectation:** Payload correctly reflects the change.

### 10. Test Data
- None required.
