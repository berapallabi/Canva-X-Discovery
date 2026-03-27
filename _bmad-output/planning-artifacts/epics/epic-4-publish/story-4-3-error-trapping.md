# Story 4.3: Error Trapping & Native Rate Limits

### 1. Core User Story Statement
**As a** Canva Creator attempting to publish consistently,
**I want the** app to clearly explain if I hit a rate limit or API block instead of crashing entirely,
**So that** I know exactly when I can return to publish my design later.

### 2. Acceptance Criteria

*   **AC1: 429 Rate Limit Visual Parsing (Error Handling)**
    *   **Given** I have tweeted too many times today (hitting the X API volume quota)
    *   **When** the Next.js Proxy receives a `429 Too Many Requests` code from X
    *   **Then** the backend MUST parse the attached `x-rate-limit-reset` Unix timestamp header natively.
    *   **And** the React UI MUST convert that Unix timestamp into a human-readable local timezone string, showing an exact explicit message: "Daily limit reached. You can publish again at 4:30 PM."

*   **AC2: Global API Outage Bridging (Negative Scenario)**
    *   **Given** X.com's API is fully offline or experiencing rolling blackouts
    *   **When** a `502` or `503` status is trapped by the backend completely organically
    *   **Then** the frontend MUST display a localized error: "X is currently experiencing downtime. Please try publishing again later." rather than a blank 500 server crash screen.

*   **AC3: User Revocation Trapping (Negative Scenario)**
    *   **Given** I revoked the app tokens inside Twitter's official security settings
    *   **When** I attempt to publish via Canva
    *   **Then** the resulting `401 Unauthorized` string must trigger the Native Canva `auth` SDK to safely delete the stored tokens locally and unmount my session to prevent recursive errors.

*   **AC4: Success URL Bridging (Happy Path)**
    *   **Given** the payload transmits and publishes flawlessly
    *   **When** X API returns a `201 Created` status
    *   **Then** the proxy MUST parse the returned Tweet ID string and format it into a valid `https://x.com/user/status/{ID}` URL.
    *   **And** the Canva plugin MUST display a Success interface providing a clickable hyperlink out to the live published post.

### 3. Business Context / Objective
Transparency in API failures preserves brand trust. Users shouldn't have to guess why a post failed.

### 4. In scope
- HTTP interceptors parsing Header strings specifically for rate-limit UNIX identifiers.
- The Success state UI block displaying the final link.

### 5. Out of Scope
- Automatic queueing or retry mechanisms (too complex without a custom database implementation).

### 6. UI/UX References
- Canva standard `Toast` error overlays or `Banner` components.

### 7. Dependencies
- Dependent on Epic 4.2 chunk loop reliably pushing the final payload to execution.

### 8. Technical Notes
Ensure rigorous TypeScript types for external payload error trapping, as X API error payloads shift depending on endpoints (v1.1 returns different error shapes than v2).

### 9. Testing scenarios
- **Test:** Mock a `429` return. **Expectation:** Front-end calculates the UTC reset time properly and displays it accurately to the user.
- **Test:** Mock a `201`. **Expectation:** A valid `x.com` URL is rendered.

### 10. Test Data
- Standard mocked X v2 /tweets error JSON payloads.
