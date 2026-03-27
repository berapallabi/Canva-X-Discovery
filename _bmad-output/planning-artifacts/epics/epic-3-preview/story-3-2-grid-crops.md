# Story 3.2: Multi-Image Grid Cropping Simulator

### 1. Core User Story Statement
**As a** Canva Creator building a composite multi-page design,
**I want to** see exactly how X will crop and physically arrange my pages in its feed layout,
**So that** my key informational text isn't accidentally cropped out of the center of the timeline view.

### 2. Acceptance Criteria

*   **AC1: Single Image Extreme Cropping (Happy Path)**
    *   **Given** I have exactly 1 page selected in Epic 2
    *   **When** the Preview simulator renders the media block
    *   **Then** the image MUST render at full width.
    *   **And** if the aspect ratio of my design is extremely tall (e.g., a 1080x1920 infographic), the simulator MUST truncate the top and bottom with hidden overflow, mimicking X's native safe-zone rules exactly.

*   **AC2: Two Image 50/50 Split (State Change)**
    *   **Given** I select exactly 2 pages
    *   **When** the Preview updates
    *   **Then** the media block MUST split vertically exactly 50/50 down the middle.
    *   **And** Page 1 must render on the left, and Page 2 must render on the right.

*   **AC3: Three Image Anchor Layout (State Change)**
    *   **Given** I select exactly 3 pages
    *   **When** the Preview updates
    *   **Then** Page 1 MUST act as the large graphical "Anchor" on the left half.
    *   **And** Pages 2 and 3 MUST stack vertically (top and bottom) on the right half.

*   **AC4: Four Image Matrix (State Change)**
    *   **Given** I select exactly 4 pages
    *   **When** the Preview updates
    *   **Then** the images MUST render as a perfect 2x2 grid layout (perfect quarters).

*   **AC5: Missing Image Artifacts (Error Handling / Edge Case)**
    *   **Given** the Canva SDK is actively generating the exported thumbnail previews
    *   **When** an image URL is pending or broken
    *   **Then** the simulator MUST show a neutral grey rectangle with a loading spinner instead of a broken image icon.

### 3. Business Context / Objective
"Center crop fail" is the number one reason brands delete and repost content on X. By doing the crop math right in front of them natively, we save them from having to manually check safe-zones.

### 4. In scope
- The CSS Grid mathematical layout engine for 1, 2, 3, and 4 object arrays.

### 5. Out of Scope
- Rendering 5+ images (X API blocks this natively).

### 6. UI/UX References
- Standard `grid-template-columns` and `aspect-ratio` CSS configurations.

### 7. Dependencies
- Dependent on Story 2.2 for the active `selectedPages` state array.

### 8. Technical Notes
Canva's `useExportMetadata()` hook dynamically returns low-res base64 generic image strings of the active pages. Use these explicitly for the preview rendering to keep memory overhead low.

### 9. Testing scenarios
- **Test:** Click pages 1, 2, and 3. **Expectation:** Preview morphs into the 3-image Anchor layout seamlessly.

### 10. Test Data
- Test heavily with varied aspect ratios (16:9, 9:16, 1:1).
