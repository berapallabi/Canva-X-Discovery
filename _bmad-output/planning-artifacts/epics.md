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

- **FR1.1-1.4:** Auth behaviors, User Onboarding, Deferred Auth, Scopes, OAuth execution.
- **FR2.1-2.3:** Authenticated Session behaviors, Data Extraction, Persistence.
- **FR3.1-3.5:** Multi-Account Context Switching and Clean Revocation.
- **FR7-12:** Drafting Captions (250 chars max), Media Selection (max 4), Subtitle/Video Caption files, Post Settings, Alt-Text, Tags, Location, Reply Controls, Content Warnings, and physical Payload Validations.
- **FR13-21:** WYSIWYG Live Previews, CSS Grid mockups, Active Blurs, Pill generation, and Responsive Light/Dark Mode testing.
- **FR22-31:** Clicking Publish, returning URL upon Success, trapping API errors, rate limits (429), and caching logic natively.

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
FR22 - FR31 (Publishing Rules): Epic 4 - Serverless Publish Execution

## Epic List

### Epic 1: Identity & Authentication Pipeline
Users can learn about the app via Onboarding, securely connect one or multiple X accounts to their Canva session, switch between them, and revoke access cleanly, forming the foundation of their identity.
**FRs covered:** FR1.1, FR1.1b, FR1.2, FR1.3, FR1.4, FR2.1, FR2.2, FR2.3, FR3.1, FR3.2, FR3.3, FR3.4, FR3.5

### Epic 2: Post Composition & Settings Panel
Users can configure their entire fixed-format post (draft text up to 250 chars, select explicit media pages, upload video caption files, add Alt-text, set reply controls, content warnings, and hit block-limits) from a single, unified Settings panel without needing to leave the editor.
**FRs covered:** FR7, FR8, FR9, FR10, FR10.1, FR10.2, FR10.3, FR10.4, FR10.5, FR10.6, FR10.7, FR10.8, FR11, FR12

### Epic 3: WYSIWYG Live Preview Simulator
Users gain absolute publishing confidence by seeing an exact, real-time X-feed simulation of their post (including complex image grid crops and CSS blurs) before they commit to publishing.
**FRs covered:** FR13, FR14, FR15, FR16, FR18, FR19, FR20, FR21

### Epic 4: Serverless Publish Execution & Error Trapping
Users can reliably push their payload to X via the background proxy, receiving a direct live URL upon success, or a human-readable local error (e.g., rate limits) upon failure.
**FRs covered:** FR22 (Publish), FR23 (Publish), FR24 (Publish), FR25 (Publish Auth), FR30, FR31

---

## Epic 1: Identity & Authentication Pipeline

**Goal:** Establish a frictionless onboarding pipeline driving user adoption via an Onboarding Page and Deferred Authentication. Ensures compliance with zero-database strictures by natively storing X tokens securely in Canva's auth infrastructure natively. Provide complex multi-account switching capabilities securely.

### Story 1.0: Build User Onboarding UI

**1. Core User Story Statement**
**As a** first-time user,
**I want** to see an onboarding page explaining the integration's value proposition,
**So that** I understand why I should connect my X account and what features are available.

**2. Acceptance Criteria**
*   **AC1: Happy Path (Primary Flow)**
    *   **Given** a user opens the app for the very first time
    *   **When** they view the plugin side-panel
    *   **Then** the UI MUST render a static Welcome/Onboarding screen.
    *   **And** it must display a Hero Graphic, Value Prop text, Privacy links, and a primary "Open" button.
*   **AC2: Dismissal**
    *   **Given** the user is on the Onboarding screen
    *   **When** they click "Open"
    *   **Then** the Onboarding screen permanently dismisses, routing the user to the Zero-State UI.

### Story 1.1: Build Zero-State Deferred Auth UI

**1. Core User Story Statement**
**As an** unauthenticated user,
**I want** to interact with the Settings UI to draft my post and preview it freely before logging in,
**So that** I don't hit an immediate OAuth hard-wall blocking my creative flow.

**2. Acceptance Criteria**

*   **AC1: Happy Path (Primary Flow)**
    *   **Given** a returning user opens the app without cached OAuth tokens
    *   **When** they view the plugin side-panel
    *   **Then** the UI MUST permit all interactions (Drafting captions up to 250 chars, Selecting Export Pages, Parsing WYSIWYG Simulator).
    *   **And** the 'Publish' CTA must be physically disabled and visually greyed out.
    *   **And** a primary "Connect to X" button MUST render prominently to gate publishing.

*(Details truncated for brevity...)*

### Story 1.2: Implement Canva OAuth Token Exchange

**1. Core User Story Statement**
**As a** user,
**I want** to securely click "Connect to X" to launch the OAuth modal and link my profile,
**So that** my profile is permanently recognized by the proxy securely.

*(Details truncated for Story 1.3 and 1.4 placeholder...)*
