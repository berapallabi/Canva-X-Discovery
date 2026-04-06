# Story 2.2: Media Export & Page Selection Manager

### 1. Core User Story Statement
**As an** authenticated Canva Creator,
**I want to** choose exactly which pages to publish as a fixed Post,
**So that** my media is correctly formatted and optimized for X's specific upload constraints.

### 2. Acceptance Criteria

*   **AC1: Fixed Format Post (Happy Path)**
    *   **Given** I am in the Settings UI
    *   **When** I interact with the media selection
    *   **Then** the format type MUST be a "Fixed format type - Post".
    *   **And** I can select Image and Video media to attach to this post.

*   **AC2: Page Selection Interaction (Happy Path)**
    *   **Given** I have a multi-page Canva document
    *   **When** I interact with the "Pages to Publish" native selector
    *   **Then** I MUST be able to explicitly check or uncheck individual pages via a visual grid.

*   **AC3: Strict Page Limits (Validation Rules)**
    *   **Given** my Canva design
    *   **When** I attempt to select more than 4 pages
    *   **Then** the UI MUST block me from selecting a 5th.
    *   **And** a small warning string MUST explain: "Maximum 4 pages per post."

*   **AC4: Video Duration Validity (Validation Rules)**
    *   **Given** I select an MP4 Video payload
    *   **When** the duration physically exceeds 140 seconds
    *   **Then** the Publish button MUST disable with an accompanying duration warning.

### 3. Business Context / Objective
X has strict file-count and format-specific limits. By enforcing max 4 pages and standardizing on a fixed post type, we align perfectly with the updated canonical workflow, removing unneeded complexity like GIF handling or multithreading from the UX.

### 4. In scope
- Canva App UI Kit `PageSelector` integration.
- Unified reactive validation hooks evaluating Image and Video sizes.

### 5. Out of Scope
- GIF exports or specific interactions surrounding GIFs (Parked).
- Threading exports (Parked).

### 6. UI/UX References
- Standard `PageSelector` component usage natively inside Canva.

### 7. Dependencies
- Dependent on Story 2.1 for shared state context.

### 8. Technical Notes
The `selectedPages` must be stored as a unified "Media Configuration" array to simplify cross-field validation checks (used in Epic 3 for the WYSIWYG crop generation).

### 9. Testing scenarios
- **Test:** Select 5 pages. **Expectation:** UI blocks 5th selection and shows warning.

### 10. Test Data
- Test with documents containing 1, 4, 5, and 20+ pages.
