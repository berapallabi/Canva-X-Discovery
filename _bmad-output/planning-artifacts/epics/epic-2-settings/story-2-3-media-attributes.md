# Story 2.3: Media Attributes (Alt-Text & Video Captions)

### 1. Core User Story Statement
**As a** Canva Creator attaching media to my post,
**I want to** add descriptive Alt-Text to images or upload `.srt` files to my videos,
**So that** my post is fully accessible and native compliant.

### 2. Acceptance Criteria

*   **AC1: Image Alt-Text Activation (Happy Path)**
    *   **Given** I have selected an Image slot in the Media Selector (Story 2.2)
    *   **When** I open the media attributes panel
    *   **Then** the UI MUST render an "Alt text" text field explicitly enabling me to describe the image.
    *   **And** it must enforce a 1000 character limit natively.

*   **AC2: Video Caption Upload Activation (Happy Path)**
    *   **Given** I have selected a Video slot in the Media Selector
    *   **When** I open the media attributes panel
    *   **Then** the UI MUST render a "Video caption file" upload field enabling me to upload subtitles.
    *   **And** the UI must validate that the file uploaded matches `.srt` or `.vtt` formats.

*   **AC3: State Synchronization**
    *   **Given** I alter my media page selection
    *   **When** pages are added or removed
    *   **Then** the corresponding Alt-Text inputs or Video Caption uploads MUST dynamically appear or disappear.

*   **AC4: Metadata Persistence (Data Handling)**
    *   **Given** I have entered Alt-Text or uploaded a `.srt`
    *   **When** the app prepares the final structure
    *   **Then** it MUST store these in a `media_metadata` context object mapped by the page numeric ID.

### 3. Business Context / Objective
Digital accessibility is non-negotiable. This story ensures we satisfy WCAG 2.1 brand compliance (via image alt-text) and modern video consumption standards (via subtitle uploads).

### 4. In scope
- Native code for `<TextArea>` targeting Image Alt-Text.
- Native code for File Upload components targeting Video `.srt` captions.

### 5. Out of Scope
- Actually processing the chunking of the video captions to the X API (Epic 4).

### 6. UI/UX References
- Standard `MultiLineInput` and simple file upload blocks from the UI kit.

### 7. Dependencies
- Dependent on **Story 2.2** for the count and type (Image vs. MP4) of selected pages.

### 8. Technical Notes
Distinguishing between Image and Video pages locally is required to dynamically swap between rendering an Alt-Text input versus a file-upload input.

### 9. Testing scenarios
- **Test:** Select an image. **Expectation:** Alt-Text box appears.
- **Test:** Select a video. **Expectation:** Caption File upload button appears natively.
