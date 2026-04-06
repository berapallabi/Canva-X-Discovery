# Story 1.0: Build User Onboarding UI

## 1. Core User Story Statement
**As a** first-time user,
**I want** to see an onboarding page explaining the integration's value proposition,
**So that** I understand why I should connect my X account and what features are available.

## 2. Acceptance Criteria

*   **AC1: Happy Path (Primary Flow)**
    *   **Given** a user opens the app for the very first time
    *   **When** they view the plugin side-panel
    *   **Then** the UI MUST render a static Welcome/Onboarding screen.
    *   **And** it must display a Hero Graphic, Value Prop text, Privacy links, and a primary "Open" button.

*   **AC2: Dismissal and Routing**
    *   **Given** the user is on the Onboarding screen
    *   **When** they click "Open"
    *   **Then** the Onboarding screen permanently dismisses.
    *   **And** the user is immediately routed to the Zero-State UI.
    *   **And** the app persists a local flag indicating the user has cleared onboarding, ensuring it never appears again for this browser session memory.

*   **AC3: Accessibility**
    *   **Given** the onboarding page renders
    *   **When** parsed by screen readers
    *   **Then** the Hero Graphic must have descriptive alt-text and all links must be fully keyboard navigable.

## 3. Business Context / Objective
To drive initial conversion, first-time users need context about the integration's permissions and functionality before diving into the configuration panel.

## 4. In scope
- Static layout for the Onboarding UI.
- Local persistence key (`hasSeenOnboarding`).

## 5. Out of Scope
- Backend metrics tracking of onboarding completion.

## 6. UI/UX References
- **PRD:** FR1.1b User Onboarding Page.

## 7. Dependencies
- Canva UI Kit Text, Link, and Button components.

## 8. Technical Notes
Use `window.localStorage` (if permitted by Canva constraints) or a simple background cookie state to track the `hasSeenOnboarding` boolean flag.

## 9. Testing Scenarios
- Launch the app with a cleared cache -> Confirm Onboarding appears.
- Click Open -> Confirm route to Zero-State.
- Close app, reload, reopen App -> Confirm Onboarding is bypassed.
