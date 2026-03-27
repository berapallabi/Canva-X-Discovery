# Story 2.5: Geographical Context (Location Search)

### 1. Core User Story Statement
**As a** Canva Creator,
**I want to** search for and attach a physical location to my post,
**So that** my content appears in native X local discovery feeds and provides context for live events.

### 2. Acceptance Criteria

*   **AC1: Location Search (Happy Path)**
    *   **Given** I am in the settings panel
    *   **When** I type into the "Add Location" search bar
    *   **Then** the app MUST trigger a debounced network request to X's `/geo/search` API endpoint through our Next.js proxy.

*   **AC2: Displaying Candidates (UI Behavior)**
    *   **Given** the Geo search returns results
    *   **When** the data arrives
    *   **Then** the app MUST render a scrollable list of location names (e.g., "Sydney, Australia").
    *   **And** clicking a location MUST store its unique `place_id` in the internal draft state.

*   **AC3: Clearing Location (Interaction)**
    *   **Given** a location is already selected
    *   **When** I click the "X" icon next to the location name
    *   **Then** the selection MUST be cleared from the state and the search bar MUST reset.

### 3. Business Context / Objective
Contextual content. Events, launches, and retail locations rely on geotags to drive foot traffic and local relevance.

### 4. In scope
- Debounced search input.
- Proxy routing for X Geo API results.
- Storing the `place_id` token.

### 5. Out of Scope
- GPS/Browser Location permission requests (We rely on text-based search to avoid privacy friction).

### 6. UI/UX References
- Standard `Search` bar with a vertical results pop-over.

### 7. Dependencies
- Dependent on Epic 1 Auth to sign the Geo Search request.

### 8. Technical Notes
X Geo `place_id` is an alphanumeric string (e.g. `df51dec6f4ee2b2c`) which MUST be passed in the final `geo.place_id` block of the v2 publish request.

### 9. Testing scenarios
- **Test:** Search "San Francisco". **Expectation:** See list of SF-related locations.
- **Test:** Click one. **Expectation:** It stays selected as a visual "pill" in the UI.

### 10. Test Data
- Global city names.
