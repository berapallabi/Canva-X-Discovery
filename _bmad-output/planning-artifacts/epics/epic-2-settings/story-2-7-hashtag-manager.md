# Story 2.7: Content Discovery (Hashtag Manager)

### 1. Core User Story Statement
**As a** Canva Creator,
**I want to** categorize my post with relevant hashtags,
**So that** my content appears in niche discovery feeds and reaches target audiences interested in specific topics.

### 2. Acceptance Criteria

*   **AC1: Hashtag Entry (Happy Path)**
    *   **Given** I am in the Settings panel
    *   **When** I locate the "Hashtags" section
    *   **Then** I MUST be able to type or select multiple hashtags.

*   **AC2: Hashtag Group Support (UI Interaction)**
    *   **Given** I want to add multiple hashtags efficiently
    *   **When** I click on a hashtag chip from the "Recent" or "Saved" list
    *   **Then** the UI MUST immediately append that hashtag to the Caption Editor (Story 2.1) state.
    *   **And** it MUST ensure there is a space character between hashtags.

*   **AC3: Hashtag Counter (Logic)**
    *   **Given** I have added 5 hashtags
    *   **When** I view the Hashtag Manager
    *   **Then** I MUST see a clear list of active tags I have chosen for this post.
    *   **And** I MUST be able to remove them individually via a small "X" icon on the chip.

### 3. Business Context / Objective
Content Categorization. Tagging the topic is as important as tagging the person; this story provides the necessary tooling for professional social media distribution.

### 4. In scope
- Hashtag chip entry UI.
- Logic to append hashtags to the Caption string.
- (Optional) Local storage persistence for "Recent" hashtags.

### 5. Out of Scope
- Global trending hashtag discovery (requires separate expensive API calls).

### 6. UI/UX References
- Standard `Tag` or `Chip` components from the Canva App UI Kit.

### 7. Dependencies
- Dependent on **Story 2.1** for text injection.

### 8. Technical Notes
Use a simple state array `hashtags: string[]` to manage the collection before formatting them into the final caption string `caption + " " + hashtags.join(" ")`.

### 9. Testing scenarios
- **Test:** Add `#design`. **Expectation:** It appears in the selected list and in the Caption.
- **Test:** Remove `#design`. **Expectation:** It is removed from the Caption.

### 10. Test Data
- Common design hashtags: `#canva`, `#graphicdesign`, `#socialmediatips`.
