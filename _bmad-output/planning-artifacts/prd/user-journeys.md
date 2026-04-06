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
  - `Value Proposition Text`: Explanation of features (e.g., *"Post directly to X and manage multiple accounts without leaving the editor."*).
  - `Terms & Privacy Links`: Clickable hyperlinks.
  - `Open` Button: Primary CTA.
- **Interaction [Click 'Open']:** The Onboarding screen permanently dismisses. The UI renders the **Zero-State Settings Panel (See Path B)**.

### Path B: Unauthenticated User (Returning but logged out)
The app operates on a strict **Deferred Authentication** paradigm. Users do NOT face a hard login wall. Instead, they see a fully functional "Zero-State" Publisher UI to demonstrate value before gating the experience.
- **Fields & Elements Available (Pre-Login):**
  - `Field 3.2 (Media Selection)`: Fully unlocked. Users can select design pages, and the app will enforce >5MB or 140s video validations locally.
  - `Field 3.3 (Caption Input)`: Fully unlocked. Users can write text, utilize `@mentions`, and view dynamic character counts.
  - `Phase 4 (Live WYSIWYG Preview)`: Fully unlocked. Visually renders the drafted layout mirroring X's native UI.
  - *(HIDDEN)*: Advanced options (User Tags, Reply restrictions, Content Warnings) and Account Selectors remain hidden until authenticated.
- **The Auth Gate:** The primary floating button at the bottom of the panel reads **"Connect to X"**.
- **Execution [Click 'Connect to X']:** 
  - The Canva SDK `auth.requestAuthentication()` opens the OAuth 2.0 popup.
  - **Alternative Outcome (Denial):** The user closes the OS popup or explicitly clicks "Deny". The popup closes. The side panel remains in the Zero-State. The user's drafted caption and media selections are perfectly preserved without disruption.
  - **Primary Outcome (Success):** X authorizes the app. The backend saves the Bearer & Refresh tokens. The UI *preserves* the user's drafted content, instantly swapping the primary button text to "Publish to X", and dynamically unlocking the Post Settings & Attributes (Transitioning to Path C).

### Path C: Authenticated State (Returning User, Active Session)
- **UI Rendered:** Bypass the Onboarding Page completely. Immediately render the core Settings configuration form fully unlocked (Phase 3).

---

## Phase 3: The Core Publisher UI (Settings Panel)

The user is now looking at the primary configuration form to build their post. The app uses a **Fixed format type - Post**, supporting images and videos up to 4 items.

### Field 3.1: Account Selector Profile
- **UI State:** A dropdown button displaying the currently active profile (X Avatar Image + `@handle` + Display Name) sourced from X's `users/me` endpoint.
- **Interaction:** User clicks the dropdown.
- **Options Available:**
  - `Select Account`: A list of all previously authorized X accounts. (Clicking one instantly swaps the active context and re-renders the Preview Panel).
  - `+ Add New Account`: Re-triggers the Phase 2 OAuth popup to append a new session.
  - `[Trash Icon] Disconnect`: Located next to each account.

### Field 3.2: Media Selection (Native Canva Selector)
- **UI State:** A thumbnail grid or checkbox list showing the pages/artboards of the current Canva design.
- **Options Available:** Checkboxes to select which pages to export.
- **Interaction & Validations:**
  - **Constraint 1:** If the user selects a 5th item. **Outcome:** Checkbox interaction is ignored. The UI renders red inline text: *"Maximum 4 pages per post."*
  - **Constraint 2:** If the designated export exceeds 5MB (Image) or 140 seconds (Video). **Outcome:** The file is selected, but a heavy red warning banner appears above it: *"Media exceeds maximum X size/duration limit. Please compress or trim."* The Publish button is force-disabled.

### Field 3.3: Caption Input (Post Caption)
- **UI State:** A `<textarea>` input for the post body description.
- **Options Available:** Standard text entry, `@mentions`, and `#hashtags`.
- **Interaction & Validations:**
  - User types text. A character counter in the bottom right corner (e.g., `21 / 250`) decrements dynamically.
  - **Constraint (Max Length):** As soon as the user types the 251st character. **Outcome:** The counter text turns bold red and shows negative numbers (`-1`). The Phase 5 Publish button is force-disabled.

### Field 3.4: Post Attributes (Media-Specific Fields)
Depending on the media selected in Field 3.2, specific attribute fields dynamically render:
- **Alt Text (Images):** If an image slot is selected, an "Alt text" input field is exposed allowing users to write an image description.
- **Video Caption (Videos):** If a video slot is selected, a "Video caption" file upload field is exposed allowing users to attach an explicit subtitle/transcription file (e.g. `.srt`) to the video.

### Field 3.5: Post Settings & Tagging
- **Location:** Users can click a "Location" field to attach a geo-tag to the post.
- **User Tagging Input:** Users can explicitly tag people at a post level (up to 10 accounts) via a dedicated component natively degrading to text mentions.
- **Reply Controls:** A dropdown with 4 options: `Everyone` (Default), `Accounts you follow`, `Only accounts you mention`, and `Verified accounts`.
- **Content Warning:** Users can set a post content warning by marking a specific category (e.g., sensitive, violence). 
- **Enable Download Video:** A toggle to allow or block users to download an attached video.

---

## Phase 4: Extreme WYSIWYG Live Preview Syncing

The Preview panel perfectly mocks the native `x.com` DOM. Every field in Phase 3 exerts a specific, dynamic mutation on the Preview, providing absolute confidence to the creator before executing.

### 4.1 Media Grid Formats (Reaction to Field 3.2)
The Preview mathematically restructures based on explicit item counts, mirroring X's native aspect-ratio crops:
- **1 Item:** Renders a sprawling 16:9 or native aspect ratio card.
- **2 Items:** Splits the preview container vertically (50/50). Both items are center-cropped into 8:9 vertical slivers.
- **3 Items:** Renders one large 8:9 vertical sliver on the left, and stacks two horizontal items on the right side.
- **4 Items:** Renders a symmetrical 2x2 grid, center-cropping all items to exact 1:1 ratios.
- **Video Overlays:** Any video items render with a static X Video UI overlay (a circular `Play` icon in the center).

### 4.2 Dynamic Overlays & Accessibility
- **Alt-Text applied:** Renders a small, black, clickable `ALT` badge pinned to the bottom left of the corresponding image in the Preview.
- **Content Warning checked:** The Preview applies a CSS `backdrop-filter: blur(20px)` over the media, stamping a native X *"The following media includes potentially sensitive content"* warning box directly over the blur.

---

## Phase 5: The Publish Execution

**Context:** The user has arranged a valid payload. All mandatory attributes are set. The "Publish to X" button is purple and clickable.

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
- **Context:** The chunked upload finishes, and X confirms the post creation.
- **System Operation:** Proxy returns the live `{tweet_id}` and `HTTP 200`.
- **UI State Transition:** The entire Settings form unmounts entirely.
- **UI Rendered (The Success Screen):**
  - A large green checkmark graphic or confetti animation.
  - Success text: *"Your design is officially live on X!"*
  - A primary button: **"View Post on X"** (hyperlinked directly to `https://x.com/[handle]/status/[tweet_id]`, opening in a target="_blank" tab).
  - A subtle secondary link: *"Publish another design"* (Clicking this completely unmounts the Success Screen and loops the user gracefully back to Phase 3, keeping their authentication token intact).
