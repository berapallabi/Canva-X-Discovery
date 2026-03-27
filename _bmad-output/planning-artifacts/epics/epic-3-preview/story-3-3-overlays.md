# Story 3.3: Rich Overlays & Accessibility Indicators

### 1. Core User Story Statement
**As a** Canva Creator adding advanced features to my post,
**I want to** visually confirm that my Alt-Text, sensitive warnings, and Video motion attributes are correctly flagged in the simulated view,
**So that** I know I didn't miss critical accessibility compliance checkboxes natively.

### 2. Acceptance Criteria

*   **AC1: Alt-Text Badge (Happy Path)**
    *   **Given** I have typed descriptive text into the Alt-Text box for Image 1 (from Epic 2.3)
    *   **When** I look at Image 1 in the WYSIWYG Simulator
    *   **Then** a small, solid-black pill containing the text "ALT" MUST render in the bottom-left corner of that specific image inside the layout.
    *   **And** Image 2 (which has an empty Alt-Text box) MUST NOT display the pill.

*   **AC2: Sensitive Content Frosted Blur (State Change)**
    *   **Given** I have checked the "Flag as Sensitive" box in Epic 2.3
    *   **When** the Preview Simulator re-renders
    *   **Then** the entire media grid MUST be overlaid with a heavy CSS `backdrop-filter: blur(20px)`.
    *   **And** a mock X warning label ("The following media includes potentially sensitive content") MUST render cleanly centered over the blur.

*   **AC3: Video Play Overlays (UI Behavior)**
    *   **Given** my chosen Output Type is `Video (MP4)`
    *   **When** the Simulator block loads my Cover Image
    *   **Then** a mocked circular "Play" button icon MUST render directly in the exact center of the media.
    *   **And** a small black pill indicating duration (e.g., "0:15") MUST render in the bottom right corner natively.

*   **AC4: GIF Overlays (UI Behavior)**
    *   **Given** my chosen Output Type is `Animated GIF`
    *   **When** the simulator renders the media block
    *   **Then** a small black pill containing the text "GIF" MUST render in the bottom-left corner cleanly.

### 3. Business Context / Objective
Accessibility and format context. Showing users that their accessibility settings actually "did something" makes them feel safe and compliant with enterprise brand demands.

### 4. In scope
- CSS positional absolute overlays.
- Frosting backdrop logic via CSS Filters.

### 5. Out of Scope
- Actually playing the video inside the simulator (It relies entirely on the static cover image).

### 6. UI/UX References
- Standard X Media overlay pills (Black background, white text, 4px border radius).

### 7. Dependencies
- Dependent on Stories 2.1 (Output Format) and 2.3 (Alt-Text / Flags) state variables.

### 8. Technical Notes
Standard `z-index` layering over the core CSS grid layout established in Story 3.2. Ensure the pill badges do not overflow the bounding box of extreme cropped images.

### 9. Testing scenarios
- **Test:** Type Alt Text for page 1. **Expectation:** See "ALT" pill specifically on the preview block for page 1.
- **Test:** Click "Sensitive Content". **Expectation:** Grid CSS updates to blurred state.

### 10. Test Data
- None required.
