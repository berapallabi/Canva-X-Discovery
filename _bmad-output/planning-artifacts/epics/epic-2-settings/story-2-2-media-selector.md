# Story 2.2: Media Export & Page Selection Manager

### 1. Core User Story Statement
**As an** authenticated Canva Creator,
**I want to** select my export format and choose exactly which pages to publish,
**So that** my media is correctly formatted and optimized for X's specific upload constraints.

### 2. Acceptance Criteria

*   **AC1: Format Selection (Primary Pivot)**
    *   **Given** I am in the Settings UI
    *   **When** I locate the "Export As" dropdown element
    *   **Then** I MUST explicitly select between `Static Image`, `Video (MP4)`, or `Animated GIF`.
    *   **And** this selection MUST dynamically update the Page Selector's validation logic below it.

*   **AC2: Page Selection Interaction (Happy Path)**
    *   **Given** I have a multi-page Canva document
    *   **When** I interact with the "Pages to Publish" native selector
    *   **Then** I MUST be able to explicitly check or uncheck individual pages via a visual grid.

*   **AC3: Static Image Grid Limits (Validation Rules)**
    *   **Given** my Output format is set to `Static Image`
    *   **When** I attempt to select more than 4 pages
    *   **Then** the UI MUST block me from selecting a 5th.
    *   **And** a small warning string MUST explain: "X only allows up to 4 images per tweet."

*   **AC4: Video Sequence Limitations (Validation / Negative Scenarios)**
    *   **Given** my Output format is set to `Video` or `Animated GIF`
    *   **When** I interact with the Page Selector
    *   **Then** the UI MUST restrict me to selecting one singular contiguous range of pages.

*   **AC5: Threading Alternative (Edge Case)**
    *   **Given** I have selected `Static Image` as the format
    *   **When** exactly 4 pages are already selected
    *   **Then** an optional toggle MUST appear: "Publish as X Thread (Multi-tweet)" to allow overflow into a thread structure.

### 3. Business Context / Objective
X has strict file-count and format-specific limits. By coupling the **Format** and **Page Selector** in one logic block, we ensure the user cannot commit to an invalid media combination.

### 4. In scope
- Format selection dropdown.
- Canva App UI Kit `PageSelector` integration.
- Unified reactive validation hooks for Format + Pages.

### 5. Out of Scope
- Extracting physical bytes (Epic 4).
- The Caption editor (Story 2.1).

### 6. UI/UX References
- Standard `Select` and `PageSelector` component usage.

### 7. Dependencies
- Dependent on Story 2.1 for shared state context.

### 8. Technical Notes
The `selectedFormat` and `selectedPages` must be stored as a unified "Media Configuration" object to simplify the cross-field validation checks.

### 9. Testing scenarios
- **Test:** Set Format to Video. Try selecting Page 1 and Page 3. **Expectation:** UI prevents non-contiguous selection.
- **Test:** Set Format to Image. Select 5 pages. **Expectation:** UI blocks 5th selection and shows warning.

### 10. Test Data
- Test with documents containing 1, 4, 5, and 20+ pages.
