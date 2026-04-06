# Story 2.6: Post Settings (Reply Controls, Warnings, Video Options)

### 1. Core User Story Statement
**As a** Canva Creator,
**I want to** configure my final Post settings including Reply Controls, Content Warnings, and Video Download Options,
**So that** I have total control over my post's safety, consumption rights, and privacy.

### 2. Acceptance Criteria

*   **AC1: Reply Controls Selection (Happy Path)**
    *   **Given** I am in the post settings panel
    *   **When** I locate the Reply Settings menu
    *   **Then** I MUST see four specific options:
        1. `Everyone` (Default)
        2. `Accounts you follow`
        3. `Only accounts you mention`
        4. `Verified accounts`

*   **AC2: Content Warnings (Happy Path)**
    *   **Given** I am in the post settings panel
    *   **When** I select "Content Warning"
    *   **Then** I MUST be able to mark a specific category for the warning (e.g., Sensitive, Violence, Nudity).

*   **AC3: Enable Video Downloads Toggle (Happy Path)**
    *   **Given** I have specifically selected a Video in my media panel
    *   **When** I view the post settings panel
    *   **Then** A UI toggle labeled "Enable download video option" MUST be visible, defaulting to ON.

*   **AC4: State Mapping (Data Handling)**
    *   **Given** I alter these settings organically
    *   **When** I prepare the final payload context
    *   **Then** all settings MUST be distinctly retained for mapping to the X API format natively.

### 3. Business Context / Objective
Giving users granular control over their final physical publishing mechanics directly in the app improves overall adoption rates safely among corporate clients.

### 4. In scope
- Canva App UI Kit `Select` or `RadioGroup` explicitly for Reply Controls & Content warnings.
- `Toggle` component explicitly for Video Downloads.

### 5. Out of Scope
- Actually processing the final API compilation natively (Epic 4).

### 6. UI/UX References
- Standard layout using basic form switches/dropdowns vertically aligned.

### 7. Dependencies
- Dependent on Epic 1 Auth and Epic 2.2 Media configuration (Video toggle must be context aware).

### 8. Testing scenarios
- **Test:** Verify video download toggle appears exclusively when Video format is exported.
