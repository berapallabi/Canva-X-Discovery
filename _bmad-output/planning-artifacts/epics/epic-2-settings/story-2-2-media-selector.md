# Story 2.2: Native Page Selector & Media Slicing

### 1. Core User Story Statement
**As an** authenticated Canva Creator,
**I want to** selectively choose exactly which pages of my Canva design I want to publish to X,
**So that** I don't accidentally tweet rough-draft pages or outtakes from my multi-page document.

### 2. Acceptance Criteria

*   **AC1: Page Selection Interaction (Happy Path)**
    *   **Given** I have an active Canva document with multiple pages
    *   **When** I interact with the "Pages to Publish" native selector component in the Settings panel
    *   **Then** I MUST be able to explicitly check or uncheck individual pages via a visual grid.

*   **AC2: Static Image Grid Limits (Validation Rules)**
    *   **Given** my Output format is set to `Static Image`
    *   **When** I attempt to select more than 4 pages
    *   **Then** the UI MUST block me from selecting a 5th.
    *   **And** a small warning string MUST explain: "X only allows up to 4 images per tweet."

*   **AC3: Threading Alternative (Edge Case)**
    *   **Given** I hit the 4-page ceiling limit
    *   **When** 4 pages are already selected
    *   **Then** an optional toggle MUST appear: "Publish as X Thread (Multi-tweet)" to allow overflow into a thread structure.

*   **AC4: Video Sequence Limitations (Validation / Negative Scenarios)**
    *   **Given** my Output format is set to `Video` or `Animated GIF`
    *   **When** I interact with the Page Selector
    *   **Then** the UI MUST restrict me to selecting one singular contiguous range of pages (since they will be stitched into a single MP4/GIF file).

### 3. Business Context / Objective
X has strict file-count limits (4 images max). By enforcing these constraints inside the Canva Editor, we avoid API errors and clarify the publishing logic for the user.

### 4. In scope
- Canva App UI Kit `PageSelector` integration.
- Reactive validation hooks that listen to selectedFormat state.

### 5. Out of Scope
- Extracting the physical bytes from the Canva editor (Epic 4).

### 6. UI/UX References
- Standard `PageSelector` component usage patterns.

### 7. Dependencies
- Dependent on Story 2.1 for the active Output Type logic.

### 8. Technical Notes
Use the `usePageSelector` native hook provided by the Canva Apps SDK to manage multi-page selection state.

### 9. Testing scenarios
- **Test:** Set Format to Video. Try selecting Page 1 and Page 3 (skipping Page 2). **Expectation:** UI prevents non-contiguous selection.
- **Test:** Set Format to Image. Select 5 pages. **Expectation:** UI blocks 5th selection and shows warning.

### 10. Test Data
- Test with documents containing 1, 4, 5, and 20+ pages.
