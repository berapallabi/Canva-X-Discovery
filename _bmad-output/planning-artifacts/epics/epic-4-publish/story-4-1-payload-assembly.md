# Story 4.1: Pre-flight Validations & The 5KB Limit

### 1. Core User Story Statement
**As a** Canva Creator attempting to publish,
**I want the** app to validate my file size and payload shape physically before initiating the actual video upload execution,
**So that** I don't waste 30 seconds loading just to get a predictable string-length or file-size error at the finish line.

### 2. Acceptance Criteria

*   **AC1: Empty Payload Prevention (Negative Scenario)**
    *   **Given** I have 0 pages selected AND my caption is empty
    *   **When** I click Publish
    *   **Then** the button MUST organically reject the attempt.
    *   **And** the UI must render a standard Canva Toast explaining "You must provide media or text to publish."

*   **AC2: 5KB Canva Limit Shield (Validation Rules)**
    *   **Given** I click "Publish to X"
    *   **When** the frontend attempts to assemble the JSON `PublishRef` object
    *   **Then** it MUST calculate the absolute byte-size of the stringified payload accurately before dispatch.
    *   **And** if the payload exceeds Canva's strict 5KB native limit (e.g., I typed extremely long Alt-Text blocks that ballooned the JSON shape), the upload MUST halt locally and present a specific warning toast.

*   **AC3: Media File Size Restrictions (Validation Rules)**
    *   **Given** I am attempting to publish a video Export
    *   **When** the app calculates the total bytes from the Canva export request
    *   **Then** it MUST block execution natively if the final MP4 file exceeds X API's absolute maximum 512MB limit, presenting a clear error to compress the video.

### 3. Business Context / Objective
Preventing 413 Payload Too Large explosions and guaranteeing rapid failure. Validating constraints mathematically inside the client removes useless traffic from the Vercel Proxy backend and saves the user time.

### 4. In scope
- Local pre-flight mathematical checks on the assembled payload.

### 5. Out of Scope
- Actually transmitting the bytes to the proxy.

### 6. UI/UX References
- Standard Canva `Toast` error system for validation failure alerts.

### 7. Dependencies
- Dependent on Epic 2's composition UI providing the raw data.

### 8. Technical Notes
Validation must happen synchronously before the core asynchronous `fetch` wrappers execute toward the proxy. Calculate byte length of JSON using `new Blob([JSON.stringify(payload)]).size`.

### 9. Testing scenarios
- **Test:** Assign an empty caption and 0 pages. **Expectation:** Publish blocked securely and toast presented.
- **Test:** Inject a mock 600MB payload. **Expectation:** Blocked seamlessly before any HTTP requests fire.

### 10. Test Data
- Edge-case JSON strings designed to exceed 5000 characters for the limit testing.
