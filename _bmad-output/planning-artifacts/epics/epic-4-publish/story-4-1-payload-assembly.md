# Story 4.1: Pre-flight Validations & Payload Assembly

### 1. Core User Story Statement
**As a** Canva Creator attempting to publish,
**I want the** app to validate my file size and compile my exact payload natively before initiating real execution routines,
**So that** I don't waste 30 seconds loading just to get a predictable limit error.

### 2. Acceptance Criteria

*   **AC1: Empty Payload Prevention (Negative Scenario)**
    *   **Given** I have 0 pages selected AND my caption is empty
    *   **When** I click Publish
    *   **Then** the button MUST organically reject the attempt.
    *   **And** the UI must render a standard Canva Toast explaining "You must provide media or text to publish."

*   **AC2: Media & Text Payload Compilation (Data Handling)**
    *   **Given** I click "Publish to X" with valid contents natively
    *   **When** the frontend attempts to assemble the JSON `PublishRef` object
    *   **Then** it MUST compile the explicit POST variables required recursively (e.g. `body`, `tags`, `reply_settings`, `<Base64_srt_file_content>`).
    *   **And** it must explicitly map string-concatenation logic for trailing user `@handles` due to the lack of API tagging fields.

*   **AC3: 5KB Canva Context Limit Shield (Validation Rules)**
    *   **Given** assembling the final shape
    *   **When** calculated
    *   **Then** if the strict payload structure itself exceeds 5KB logically, block execution natively and trigger local toast warnings.

*   **AC4: Media File Size Limits (Validation Rules)**
    *   **Given** I export an MP4 or image package natively
    *   **When** checking limits
    *   **Then** logic MUST block execution seamlessly if MP4 exceeds X's explicit 512MB structural boundaries natively.

### 3. Business Context / Objective
Preventing 413 Payload Too Large explosions and guaranteeing rapid failure. Validating constraints mathematically inside the client removes useless traffic from the Vercel Proxy backend and saves the user time.

### 4. In scope
- Local pre-flight mathematical checks on the assembled payload.
- Assembling the JSON structure with the settings payload (Reply config, Download Video bool, SRT base64 strings).

### 5. Out of Scope
- Actually transmitting the bytes to the proxy.

### 6. UI/UX References
- Standard Canva `Toast` error system for validation failure alerts.

### 7. Dependencies
- Dependent on Epic 2's composition UI providing the raw data natively.

### 8. Technical Notes
Validation must happen synchronously before the core asynchronous `fetch` wrappers execute toward the proxy. Calculate byte length of JSON using `new Blob([JSON.stringify(payload)]).size`. Be extremely careful to properly encode `.srt` subtitle files into base64 or safe JSON-compliant text blocks during payload assembly natively.

### 9. Testing scenarios
- **Test:** Assign an empty caption and 0 pages. **Expectation:** Publish blocked securely and toast presented.
- **Test:** Inject a mock 600MB payload. **Expectation:** Blocked seamlessly before any HTTP requests fire.

### 10. Test Data
- Edge-case JSON strings designed to exceed limits explicitly.
