# User Journeys

# Exhaustive End-to-End User Journey (Click-by-Click Map)

This document explicitly defines the entire sequence of events, available fields, user interactions, and specific system outcomes from the moment a user finishes a Canva design to the moment it is live on X. Every validation rule from the `field-validations-and-limitations.md` matrix is actively enforced here.

---

## Phase 1: Editor Entry & App Discovery

**Context:** The user has finished editing a design in the Canva Workspace.

1. **Interaction:** User clicks the **"Share"** button in the top right corner of the Canva Editor.
2. **Outcome:** Canva's native sharing dropdown opens.
3. **Interaction:** User scrolls and clicks **"Share on social"** or clicks **"... More"**.
4. **Interaction:** User finds and clicks the branded **"X" (Twitter)** App Icon.
5. **System Operation:** Canva initializes the Content Publisher App and slides open the right-hand App Side Panel.

---

## Phase 2: Authentication State Routing

**System Operation:** As the panel mounts, the app calls Canva's native `auth.getTokens()` to verify if the user's Canva ID is already mapped to valid X OAuth credentials.

### Path A: Unauthenticated State (First-Time User / Logged Out)
- **UI Rendered:** A static welcome screen.
- **Fields & Elements Available:**
  - `App Logo & Hero Text`: Static marketing copy explaining the value of the integration.
  - `Connect to X` Button: Primary call-to-action.
  - `Terms of Service & Privacy Policy`: Text hyperlinks opening in a new browser tab.
- **Interaction:** User clicks **"Connect to X"**.
- **System Operation:** App calls `auth.requestAuthentication()`. A secure pop-up window opens pointing to X's OAuth 2.0 authorization screen. The required scopes (`tweet.write`, `offline.access`, etc.) are explicitly requested by X.
- **Alternative Outcome 1 (Denial):** User closes the pop-up or clicks "Deny". The pop-up closes. The side panel remains in the Unauthenticated State.
- **Primary Outcome (Success):** User clicks "Authorize app". X returns the tokens. The Canva backend securely saves the Bearer & Refresh tokens. The UI automatically transitions to **Phase 3: Authenticated State**.

### Path B: Authenticated State (Returning User)
- **UI Rendered:** Bypass the welcome screen entirely. Immediately render the core Settings configuration form.

---

## Phase 3: The Core Publisher UI (Settings Panel)

The user is now looking at the primary configuration form to build their post.

### Field 3.1: Account Selector Profile
- **UI State:** A dropdown button displaying the currently active profile (X Avatar Image + `@handle` + Display Name) sourced from X's `users/me` endpoint.
- **Interaction:** User clicks the dropdown.
- **Options Available:**
  - `Select Account`: A list of all previously authorized X accounts. (Clicking one instantly swaps the active context and re-renders the Preview Panel).
  - `+ Add New Account`: Re-triggers the Phase 2 OAuth popup to append a new session.
  - `[Trash Icon] Disconnect`: Located next to each account.
- **Exception Flow (Disconnect):** If user clicks the trash icon, Canva renders a native "Are you sure?" modal. If confirmed, the backend executes an explicit token-destruction API request. If no accounts remain, the user is routed back to Phase 2.

### Field 3.2: Media Selection (Native Canva Selector)
- **UI State:** A thumbnail grid or checkbox list showing the pages/artboards of the current Canva design.
- **Options Available:** Checkboxes to select which pages to export.
- **Interaction & Validations:**
  - **Constraint 1:** If the user selects a 5th image. **Outcome:** Checkbox interaction is ignored. The UI renders red inline text: *"Maximum 4 images per tweet."*
  - **Constraint 2:** If the user selects an MP4 video, then tries to click an image. **Outcome:** Checkbox interaction is ignored. Inline red text: *"Cannot mix videos with static images."*
  - **Constraint 3:** If the designated export exceeds 5MB (Image) or 140 seconds (Video). **Outcome:** The file is selected, but a heavy red warning banner appears above it: *"Media exceeds maximum X size/duration limit. Please compress or trim."* The Phase 5 Publish button is force-disabled.

### Field 3.3: Caption Input
- **UI State:** A large `<textarea>` input for the post body.
- **Options Available:** Standard text entry, `@mentions`, and `#hashtags`.
- **Interaction & Validations:**
  - User types text. A character counter in the bottom right corner (e.g., `21 / 280`) decrements dynamically.
  - **Constraint (Max Length):** As soon as the user types the 281st character. **Outcome:** The counter text turns bold red and shows negative numbers (`-14`). The Phase 5 Publish button is force-disabled.

### Field 3.4: Add Thread Toggle
- **UI State:** A subtle text-link button: `+ Add another tweet`.
- **Interaction:** User clicks the toggle.
- **Outcome:** A secondary, identical **Media Selection (3.2)** and **Caption Input (3.3)** block appears below the first one, visually linked by a vertical thread line.
- **Constraint (Max Depth):** If the user clicks this 25 times. **Outcome:** The toggle disappears to prevent exceeding X's 25-tweet thread limit.

### Field 3.5: Advanced Options (Accordion)
- **UI State:** A collapsible menu labeled `Advanced settings ⚙️`. Defaults to closed.
- **Interaction:** User clicks to open.
- **Sub-Fields Revealed:**
  1. **Alt-Text Inputs:** One text field dynamically generated for *each* image selected in 3.2. (Max 1000 characters enforced natively by `<input maxLength>`).
  2. **User Tagging Input:** A typeahead search bar. (If user adds an 11th tag to the same image -> UI throws toast error: *"Max 10 tags per image."*).
  3. **Reply Controls Dropdown:** Options: `Everyone` (Default), `Accounts you follow`, `Only accounts you mention`.
  4. **Content Warning Checkbox:** Label: *"Flag media as sensitive"*. (If checked, applies an immediate blur-filter to the image inside the Preview Panel).

---

## Phase 4: WYSIWYG Live Preview Panel

- **UI State:** Rendered in a dedicated "Preview" tab or stacked beneath the form fields.
- **Behavior (Read-Only State Machine):** This panel is not interacted with directly. It is a live reflection of Phases 3.1 through 3.5. 
- As the user types in 3.3, configures fields in 3.5, or selects an account in 3.1, the Preview perfectly maps the data into a pixel-perfect CSS mockup of the native `x.com` feed (including Dark Mode inversion based on the user's Canva OS settings).

---

## Phase 5: The Publish Execution

**Context:** The user has arranged a valid payload. The "Publish to X" button is purple and clickable.

1. **Interaction:** User clicks the primary **`Publish to X`** CTA.
2. **UI State Transition:** The entire Settings form disables (grayed out). The publish button converts into a loading spinner labeled *"Publishing to X..."*.
3. **System Operation:** Canva compiles the design, fires `publishContent()`, and securely POSTs the payload to the Next.js Serverless Proxy.

### The Backend Router (Evaluations & Negative Outcomes)
The proxy processes the request. The UI waits for one of four specific HTTP responses to dictate the final state:

- **Exception 1 (Token Expired/Revoked on X.com):** The proxy receives a `401 Unauthorized` from X. 
  - **Outcome:** The proxy sends a 401 to the frontend. The UI spinner stops. The UI completely wipes the local Canva session array, forcing a logout. The panel resets to Phase 2 (Unauthenticated) with a red banner: *"Your session expired or was revoked. Please reconnect your account."*
- **Exception 2 (Rate Limit Exhausted):** The proxy receives a `429 Too Many Requests`.
  - **Outcome:** The proxy parses the `x-rate-limit-reset` header. The UI spinner stops. Red banner renders above the form: *"Daily limit reached. You can publish again at 14:30 PM."* The form re-enables, but the Publish button remains disabled.
- **Exception 3 (X API Global Outage):** The proxy receives a `502 Bad Gateway` or `503 Service Unavailable`.
  - **Outcome:** The UI spinner stops. Red banner renders: *"X is currently experiencing downtime. Please try again later."* Form re-enables.
- **Exception 4 (Vercel Process Timeout):** A massive video upload takes longer than 60 seconds without properly chunking.
  - **Outcome:** The UI catches a network timeout. Red banner renders: *"Upload timed out. Please compress your video and try again."*

### The Happy Path (HTTP 200 Success)
- **Context:** The chunked upload finishes, and X confirms the tweet creation.
- **System Operation:** Proxy returns the live `{tweet_id}` and `HTTP 200`.
- **UI State Transition:** The entire Settings form unmounts entirely.
- **UI Rendered (The Success Screen):**
  - A large green checkmark graphic or confetti animation.
  - Success text: *"Your design is officially live on X!"*
  - A primary button: **"View Post on X"** (hyperlinked directly to `https://x.com/[handle]/status/[tweet_id]`, opening in a target="_blank" tab).
  - A subtle secondary link: *"Publish another design"* (Clicking this completely unmounts the Success Screen and loops the user gracefully back to Phase 3, keeping their authentication token intact).
