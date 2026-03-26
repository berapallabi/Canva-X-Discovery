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

## Phase 2: Navigation & Onboarding States

**System Operation:** As the Canva X Publisher is launched from the Share Menu, the app calls `auth.getTokens()` to evaluate the user's history and current session state.

### Path A: First-Time User (Never installed or opened the app)
- **UI Rendered (Onboarding Page):** A static, dedicated welcome screen specifically designed to introduce the integration before the user touches any interactive controls.
- **Fields & Elements:**
  - `Hero Graphic`: High-quality Canva + X feature image.
  - `Value Proposition Text`: Explanation of features (e.g., *"Post directly to X, build threads, and manage multiple accounts without leaving the editor."*).
  - `Terms & Privacy Links`: Clickable hyperlinks.
  - `Open` Button: Primary CTA.
- **Interaction [Click 'Open']:** The Onboarding screen permanently dismisses. The UI renders the **Zero-State Settings Panel (See Path B)**.

### Path B: Unauthenticated User (Returning but logged out)
The app operates on a strict **Deferred Authentication** paradigm. Users do NOT face a hard login wall. Instead, they see a fully functional "Zero-State" Publisher UI to demonstrate value before gating the experience.
- **Fields & Elements Available (Pre-Login):**
  - `Hero Banner`: A dismissible banner ("Connect to X to unlock threaded publishing and premium features") positioned above the form.
  - `Field 3.2 (Media Selection)`: Fully unlocked. Users can select design pages, and the app will enforce >5MB or 140s video validations locally.
  - `Field 3.3 (Caption Input)`: Fully unlocked. Users can write text, utilize `@mentions`, and view dynamic character counts.
  - `Phase 4 (Live WYSIWYG Preview)`: Fully unlocked. Visually renders the drafted layout mirroring X's native UI.
  - *(HIDDEN)*: Advanced options (User Tags, Reply restrictions, Content Warnings, Threading) and Account Selectors remain hidden until authenticated.
- **The Auth Gate:** The primary floating button at the bottom of the panel reads **"Connect to X to Publish"**.
- **Execution [Click 'Connect to X to Publish']:** 
  - The Canva SDK `auth.requestAuthentication()` opens the OAuth 2.0 popup.
  - **Alternative Outcome (Denial):** The user closes the OS popup or explicitly clicks "Deny". The popup closes. The side panel remains in the Zero-State. The user's drafted caption and media selections are perfectly preserved without disruption.
  - **Primary Outcome (Success):** X authorizes the app. The backend saves the Bearer & Refresh tokens. The UI *preserves* the user's drafted content, instantly swapping the primary button text to "Publish to X", and dynamically unlocking the Advanced Options (Transitioning to Path C).

### Path C: Authenticated State (Returning User, Active Session)
- **UI Rendered:** Bypass the Onboarding Page and Hero Banner completely. Immediately render the core Settings configuration form fully unlocked (Phase 3).

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

## Phase 4: Extreme WYSIWYG Live Preview Syncing

The Preview panel perfectly mocks the native `x.com` DOM. Every field in Phase 3 exerts a specific, dynamic mutation on the Preview, providing absolute confidence to the creator before executing.

### 4.1 Media Grid Formats (Reaction to Field 3.2)
The Preview mathematically restructures based on explicit image counts, mirroring X's native aspect-ratio crops:
- **1 Image selected:** Renders a sprawling 16:9 or native aspect ratio card.
- **2 Images selected:** Splits the preview container vertically (50/50). Both images are center-cropped into 8:9 vertical slivers.
- **3 Images selected:** Renders one large 8:9 vertical sliver on the left, and stacks two 16:9 horizontal images on the right side.
- **4 Images selected:** Renders a symmetrical 2x2 grid, center-cropping all images to exact 16:9 ratios.
- **Video/GIF selected:** Renders the media full-bleed with a static X Video UI overlay (a circular `Play` icon in the center, and a black `duration` badge pinned to the bottom right corner).

### 4.2 Multi-Account Cascading Effects (Reaction to Field 3.1)
When the user switches accounts in the Dropdown (e.g., from a Standard to a Premium X Account):
- The Avatar and `@handle` at the top of the Preview sync instantly.
- The UI triggers a silent background check against the X API to verify the newly selected account's subscription tier. If Premium is confirmed, the Caption Field (3.3) limits dynamically explode from `280` to `25,000`, instantly removing any red constraint errors in the UI.

### 4.3 Advanced Field Mutations (Reaction to Field 3.5)
- **Alt-Text applied:** Renders a small, black, clickable `ALT` badge pinned to the bottom left of the corresponding image in the Preview.
- **Content Warning checked:** The Preview applies a CSS `backdrop-filter: blur(20px)` over the media, stamping a native X *"The author flagged this as sensitive"* warning box directly over the blur.
- **Thread Toggle active:** The Preview injects a 2px solid grey vertical line `#CFD9DE` originating from the bottom of the primary post's Avatar, connecting downward to the Avatar of the secondary thread post rendered precisely below it.

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
