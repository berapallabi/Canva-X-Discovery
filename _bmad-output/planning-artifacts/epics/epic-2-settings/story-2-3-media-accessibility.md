# Story 2.3: Media Accessibility (Alt-Text)

### 1. Core User Story Statement
**As a** Canva Creator adding media to my post,
**I want to** add descriptive Alt-Text to every image or video I select,
**So that** my post is fully accessible to visually impaired followers and satisfies WCAG 2.1 brand compliance standards.

### 2. Acceptance Criteria

*   **AC1: Dynamic Alt-Text Box Generation (Happy Path)**
    *   **Given** I have selected 2 pages in the Media Selector (Story 2.2)
    *   **When** I open the Accessibility section
    *   **Then** the UI MUST render exactly 2 distinct multi-line text boxes, each clearly labeled "Alt-Text for Page [N]".
    *   **And** changing my page selection MUST immediately add/remove these boxes to mirror the current media count.

*   **AC2: Character Counting & Validation (Validation Rules)**
    *   **Given** I am typing Alt-Text for Page 1
    *   **When** I exceed 1000 characters
    *   **Then** the UI MUST physically block further input.
    *   **And** a red counter MUST appear indicating the limit has been hit.

*   **AC3: Alt-Text Metadata Persistence (Data Handling)**
    *   **Given** I have entered Alt-Text for 3 images
    *   **When** the app prepares the final `PublishRef` payload
    *   **Then** it MUST store these strings in a `media_metadata` object mapped by the page numeric ID.
    *   **And** these strings MUST persist across the Login boundary (Story 1.2).

### 3. Business Context / Objective
Digital accessibility is non-negotiable for enterprise and government clients. This story ensures the Canva-to-X app meets standard accessibility requirements by allowing per-image metadata.

### 4. In scope
- Native code for dynamic list rendering of `<TextArea>` components.
- Live character counting logic (1000 char limit per X platform rules).

### 5. Out of Scope
- Actually binding the Alt-Text to the uploaded bytes via the `/media/metadata/create` API call (Epic 4).

### 6. UI/UX References
- Standard `MultiLineInput` with character counter below.

### 7. Dependencies
- Dependent on **Story 2.2** for the count of selected pages.

### 8. Technical Notes
The state should be stored as a `Record<number, string>` where the key is the page index. This allows the Preview Simulator (Epic 3) to easily check if it should render the "ALT" pill.

### 9. Testing scenarios
- **Test:** Select 1 page. **Expectation:** 1 Alt-Text box appears.
- **Test:** Unselect page. **Expectation:** Box disappears.
- **Test:** Type 1001 chars. **Expectation:** Input is blocked.

### 10. Test Data
- Long descriptive strings for image content.
