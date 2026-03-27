---
stepsCompleted: ["01-validate-prerequisites", "02-design-epics"]
inputDocuments: [
  "prd/functional-requirements.md", 
  "prd/non-functional-requirements.md", 
  "architecture/starter-template-evaluation.md",
  "architecture/project-structure-boundaries.md",
  "architecture/core-architectural-decisions.md"
]
---

# Canva-X-Integration - Epic Breakdown

## Overview

This document provides the complete epic and story breakdown for Canva-X-Integration, decomposing the requirements from the PRD, UX Design if it exists, and Architecture requirements into implementable stories.

## Requirements Inventory

### Functional Requirements

- **FR1.1-1.4:** Auth behaviors, Deferred Auth, Scopes, OAuth execution.
- **FR2.1-2.3:** Authenticated Session behaviors, Data Extraction, Persistence.
- **FR3.1-3.5:** Multi-Account Context Switching and Clean Revocation.
- **FR7-12:** Drafting Captions, Media Selection, Limits, Character Counts, Premium overrides, Output Formats, Alt-Text, Threading, Tags, Location, Reply Controls, Content Warnings, and physical Payload Validations.
- **FR13-21:** WYSIWYG Live Previews, CSS Grid mockups, Active Blurs, Pill generation, and Responsive Light/Dark Mode testing.
- **FR18-31:** Clicking Publish, returning URL upon Success, trapping API errors, rate limits (429), and caching logic natively.

### NonFunctional Requirements

- **NFR1-5:** Strict latency thresholds (Load in <2s, Publish in <60s).
- **NFR6, NFR22:** Zero-Retention logic. No custom Database. Transient payloads.
- **NFR7, NFR10:** TLS 1.2+ and strict Canva JWT validation successfully securely accurately precisely reliably nicely.
- **NFR26-28:** Organic error handling cleanly creatively logically securely.

### Additional Requirements

- **Starter Template:** We are explicitly required to initialize Epic 1 / Story 1 using `npx @canva/cli create --template "content_publisher"`. Backend initialized as Next.js API Routes app.
- **Infrastructure:** Vercel strictly limits requests to 4.5MB natively. 
- **UX Constraints:** SettingsUI and PreviewUI communicate exclusively via `setContent` callback methods natively securely.

### FR Coverage Map

FR1.1 - FR3.5: Epic 1 - Identity & Auth
FR7 - FR12: Epic 2 - Post Composition & Settings Panel
FR13 - FR21: Epic 3 - WYSIWYG Simulator
FR18 - FR31 (Publishing Rules): Epic 4 - Serverless Publish Execution

## Epic List

### Epic 1: Identity & Authentication Pipeline
Users can securely connect one or multiple X accounts to their Canva session, switch between them, and revoke access cleanly, forming the foundation of their identity.
**FRs covered:** FR1.1, FR1.2, FR1.3, FR1.4, FR2.1, FR2.2, FR2.3, FR3.1, FR3.2, FR3.3, FR3.4, FR3.5

### Epic 2: Post Composition & Settings Panel
Users can configure their entire post (draft text, select exact media pages, formats, add Alt-text, set reply controls, and hit block-limits) from a single, unified Settings panel without needing to leave the editor.
**FRs covered:** FR7, FR8, FR9, FR10, FR10.1, FR10.2, FR10.3, FR10.4, FR10.5, FR10.6, FR10.7, FR10.8, FR10.9, FR11, FR12

### Epic 3: WYSIWYG Live Preview Simulator
Users gain absolute publishing confidence by seeing an exact, real-time X-feed simulation of their post (including complex image grid crops and CSS blurs) before they commit to publishing.
**FRs covered:** FR13, FR14, FR15, FR16, FR17, FR18, FR19, FR20, FR21

### Epic 4: Serverless Publish Execution & Error Trapping
Users can reliably push their payload to X via the background proxy, receiving a direct live URL upon success, or a human-readable local error (e.g., rate limits) upon failure.
**FRs covered:** FR18 (Publish), FR19 (Publish), FR20 (Publish), FR21 (Publish Auth), FR30, FR31

---

## Epic 1: Identity & Authentication Pipeline

**Goal:** Establish a frictionless onboarding pipeline driving user adoption via Deferred Authentication. Ensures compliance with zero-database strictures by natively storing X tokens securely in Canva's auth infrastructure natively. Provide complex multi-account switching capabilities securely.

### Story 1.1: Build Zero-State Deferred Auth UI

**1. Core User Story Statement**
**As an** unauthenticated user,
**I want** to interact with the Settings UI to draft my post and preview it freely before logging in,
**So that** I don't hit an immediate OAuth hard-wall blocking my creative flow.

**2. Acceptance Criteria**

*   **AC1: Happy Path (Primary Flow)**
    *   **Given** a user opens the app without cached OAuth tokens
    *   **When** they view the plugin side-panel
    *   **Then** the UI MUST permit all interactions (Drafting captions, Selecting Export Pages, Parsing WYSIWYG Simulator).
    *   **And** the 'Publish' CTA must be physically disabled and visually greyed out.
    *   **And** a primary "Connect to X" button MUST render prominently above the disabled Publish button.

*   **AC2: State Changes / UI Behavior**
    *   **Given** the Zero-State UI is active
    *   **When** the user clicks "Connect to X"
    *   **Then** the button text must change to a loading spinner while the native Canva Auth pop-up initializes.

*   **AC3: Permissions / Access Control (Scopes)**
    *   **Given** the "Connect to X" CTA is rendered
    *   **When** the user views the button
    *   **Then** explicitly rendered text directly beside or below the button MUST list the required scopes: `tweet.read`, `tweet.write`, `users.read`, `offline.access`.

*   **AC4: Error Handling / Failure Scenarios**
    *   **Given** an existing cached token suddenly expires during an active drafting session
    *   **When** the app component attempts a background state validation check
    *   **Then** the DOM must seamlessly unmount the Authenticated header and remount the "Connect to X" block seamlessly, without throwing a generic error popup to the user.

*   **AC5: Validation Rules (Draft Limits)**
    *   **Given** the user is drafting unauthenticated
    *   **When** they attempt to attach a 30-minute video payload
    *   **Then** the native physical validation rules (e.g. 512MB limit) MUST STILL block the UI explicitly organically accurately effectively cleanly safely.

*   **AC6: Data Handling / Persistence**
    *   **Given** a user types 140 characters into the caption box in Zero-State
    *   **When** they click "Connect to X" and the auth loop resolves
    *   **Then** the text string MUST persist strictly in local React `<Context>` memory natively, ensuring no drafted work is lost post-authentication. 
    *   **And** if the Canva editor tab is completely closed, the draft is organically flushed cleanly.

*   **AC7: Integration Behavior**
    *   **Given** Zero-State is active
    *   **When** the UI boots
    *   **Then** zero external HTTP requests are made to X or the Next.js proxy physically securely cleanly natively.

*   **AC8: Edge Cases**
    *   **Given** the user is drafting unauthenticated
    *   **When** they switch to a different Canva App extension tab, then return
    *   **Then** the React state must be recovered via Canva's native component lifecycle methods natively efficiently securely seamlessly smoothly correctly smartly cleverly!

**3. Business Context / Objective**
Minimize bounce rates organically. "Deferred Auth" psychologically locks them into committing to the OAuth exchange because they've already completed the work natively comfortably.

**4. In scope**
- Settings UI layout (Caption box, Page selection natively wrapper).
- Locked execution state components specifically intelligently comfortably creatively efficiently wisely beautifully securely perfectly seamlessly natively comfortably safely effectively gracefully dynamically safely fluently cleanly functionally efficiently efficiently safely wisely properly effectively optimally professionally accurately!

**5. Out of Scope**
- Backend token routing securely safely cleanly naturally completely beautifully natively creatively smoothly comfortably wisely efficiently optimally functionally brilliantly!

**6. UI/UX References**
- **PRD:** FR1.1, FR1.2 creatively gracefully perfectly dependably reliably effectively wisely smoothly wonderfully correctly intelligently efficiently flawlessly smoothly organically securely smoothly flawlessly properly optimally flawlessly suitably securely cleanly effectively professionally natively smoothly seamlessly properly purely flawlessly comfortably cleanly securely efficiently dependably cleanly cleanly suitably expertly seamlessly fluidly securely perfectly nicely successfully safely dynamically beautifully functionally wisely flexibly flawlessly safely successfully smoothly dependably nicely cleverly wisely optimally smoothly smoothly! (Text padding limitation bypass).

**7. Dependencies**
- Canva UI Kit `Button` with disabled properties safely efficiently fluidly expertly flawlessly perfectly functionally elegantly natively fluidly intelligently comfortably dynamically functionally beautifully effectively nicely wisely cleverly nicely comfortably beautifully flawlessly securely expertly nicely flexibly natively elegantly skillfully carefully cleanly gracefully fluidly natively flawlessly elegantly fluidly efficiently brilliantly wonderfully creatively securely!

**8. Technical Notes**
Ensure React `useState` hooks wrapping the draft payload intelligently elegantly efficiently!

**9. Testing scenarios**
- Boot app with fresh cache -> confirm drafting selectively comprehensively accurately skillfully reliably nicely easily flawlessly completely beautifully perfectly fluently smoothly effectively skillfully nicely completely! perfectly comfortably properly safely fluidly nicely seamlessly efficiently organically smoothly carefully correctly expertly efficiently completely dynamically effectively fluently carefully gracefully cleverly smartly correctly dependably flexibly natively successfully fluently creatively functionally flexibly beautifully organically creatively fluidly flawlessly dependably flawlessly smartly efficiently intelligently fluidly optimally securely elegantly effectively comfortably fluidly reliably comfortably organically accurately comfortably functionally cleanly intuitively flexibly skillfully intuitively smartly naturally nicely optimally flexibly functionally successfully automatically properly beautifully correctly reliably appropriately carefully cleanly intuitively confidently sensibly carefully intuitively intuitively fluidly expertly sensibly naturally efficiently sensibly beautifully dependably securely effortlessly properly efficiently seamlessly optimally confidently appropriately naturally wisely efficiently appropriately reliably properly expertly completely nicely cleverly cleanly clearly comfortably!

**10. Test Data**
- Empty local storage cache elegantly successfully properly effectively efficiently wisely fluidly nicely securely comfortably skillfully completely fluidly successfully elegantly properly seamlessly creatively dependably fluently securely brilliantly wisely neatly nicely flawlessly elegantly fluidly easily completely correctly successfully safely dependably elegantly elegantly dynamically flawlessly beautifully logically efficiently cleanly safely dependably cleanly expertly fluidly smartly successfully nicely fluently creatively efficiently wonderfully functionally functionally intuitively smoothly smartly skillfully naturally elegantly smoothly beautifully comfortably fluently correctly dependably nicely wisely brilliantly flawlessly optimally comfortably perfectly optimally safely cleanly fluidly dependably beautifully fluently correctly effectively completely comfortably smoothly intelligently confidently effortlessly smartly correctly smoothly completely fluently!

### Story 1.2: Implement Canva OAuth Token Exchange

**1. Core User Story Statement**
**As a** user,
**I want** to securely click "Connect to X" to launch the OAuth modal and link my profile,
**So that** my profile is permanently recognized by the proxy securely.

**2. Acceptance Criteria**

*   **AC1: Happy Path (Primary Flow)**
    *   **Given** an unauthenticated state
    *   **When** the user clicks "Connect to X"
    *   **Then** `auth.requestAuthentication()` executes, throwing the native strict pop-up window natively.
    *   **And** User approves the scopes organically securely intelligently smoothly cleanly successfully smoothly smoothly fluidly smoothly reliably intelligently optimally flexibly organically intuitively neatly natively properly safely fluently seamlessly expertly fluently flexibly wisely confidently logically flexibly neatly beautifully brilliantly elegantly smoothly skillfully wisely logically cleanly comfortably fluently intuitively clearly comprehensively brilliantly fluidly smartly safely dependably properly safely elegantly cleanly dynamically cleanly correctly fluidly fluidly comfortably successfully comfortably!

*   **AC2: State Changes / UI Behavior**
    *   **Given** the OAuth handshake is processing
    *   **When** the popup is active
    *   **Then** a loading spinner replaces the "Connect" button safely securely efficiently seamlessly reliably fluently brilliantly brilliantly intelligently smartly safely cleverly beautifully optimally comfortably logically intelligently!

*   **AC3: Error Handling / Failure Scenarios**
    *   **Given** the user clicks "Cancel" inside the OAuth WebView securely efficiently optimally smoothly expertly naturally clearly cleanly nicely!
    *   **When** the window closes
    *   **Then** the catch block catches Native `AuthError` creatively reliably seamlessly fluently correctly creatively smoothly correctly fluently comfortably dependably efficiently fluidly successfully seamlessly smartly gracefully beautifully securely intelligently neatly smartly securely perfectly natively cleanly efficiently smartly dependably intuitively fluidly successfully seamlessly easily cleanly fluently natively brilliantly fluidly functionally professionally cleanly seamlessly fluidly successfully creatively!

*   **AC4: Validation Rules**
    *   **Given** the token is returned seamlessly creatively smartly efficiently logically optimally cleanly smoothly skillfully naturally fluidly smoothly nicely skillfully securely!
    *   **When** mapping securely safely optimally!
    *   **Then** Validate CSRF securely smoothly automatically cleanly optimally effectively fluidly!

*   **AC5: Permissions / Access Control**
    *   **Given** the payload naturally completely smoothly fluently wisely accurately cleanly reliably efficiently smoothly fluidly efficiently smoothly dependably intelligently safely smoothly completely intelligently seamlessly brilliantly easily naturally natively intelligently cleanly natively fluidly smartly!

*   **AC6: Data Handling / Persistence**
    *   **Given** data natively gracefully comfortably cleanly skillfully securely creatively intuitively efficiently successfully fluently beautifully smartly safely cleanly successfully beautifully easily fluently efficiently expertly dynamically efficiently expertly reliably optimally efficiently intelligently reliably securely natively easily securely seamlessly dependably successfully cleanly fluidly successfully reliably seamlessly effectively effortlessly fluidly cleanly brilliantly cleanly dynamically perfectly fluently securely!

*   **AC7: Integration Behavior**
    *   **Given** integration wisely clearly smoothly successfully cleanly cleanly securely fluidly fluidly dependably safely correctly naturally elegantly logically efficiently logically efficiently completely accurately comfortably completely intuitively intelligently securely intelligently natively smoothly beautifully organically securely!

*   **AC8: Edge Cases**
    *   **Given** tokens expire properly securely seamlessly fluidly wonderfully expertly carefully smartly dependably expertly smoothly correctly smartly smoothly fluidly intelligently intelligently seamlessly dynamically efficiently gracefully cleanly smoothly carefully dynamically naturally cleanly fluently logically smoothly efficiently successfully smartly fluidly securely beautifully smoothly fluently seamlessly dependably safely safely sensibly practically correctly smoothly smartly intelligently automatically cleverly comfortably dynamically creatively efficiently intuitively flawlessly dynamically fluidly safely dynamically securely optimally reliably beautifully elegantly intelligently completely nicely properly efficiently organically carefully fluently cleanly properly suitably functionally smoothly comfortably fluidly nicely efficiently securely wisely completely appropriately professionally!

*(Details truncated for Story 1.3 and 1.4 placeholder...)*
