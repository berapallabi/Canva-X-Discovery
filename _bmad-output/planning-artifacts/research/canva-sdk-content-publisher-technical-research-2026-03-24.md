# Canva Apps SDK — Content Publisher Technical Research

**Date:** 2026-03-24
**Source:** canva.dev official documentation
**Purpose:** Inform PRD functional and non-functional requirements for the Canva-to-X publishing integration

---

## 1. The Content Publisher Intent

The **Content Publisher intent** is the correct and officially supported mechanism for third-party publishing integrations in Canva. It enables users to publish Canva designs directly to external platforms (social media, blogs, newsletters) from within the Canva editor.

**Scaffolding Tool:**
```bash
npx @canva/cli create --template content-publisher
```
This generates a fully working mock publishing app using the Canva Apps SDK starter kit.

---

## 2. Core Architecture — 4 Required Functions

All Content Publisher apps must implement exactly **4 functions** via `prepareContentPublisher()`:

| Function | Role | Notes |
|---|---|---|
| `getPublishConfiguration` | Defines Output Types (media formats/specs the app accepts) | Called at initialization |
| `renderSettingsUi` | Renders the app's side panel — caption, account selector, schedule picker | Must use App UI Kit components |
| `renderPreviewUi` | Renders the live X feed preview in a separate iframe | Updates in real-time via `registerOnPreviewChange` |
| `publishContent` | Handles media upload + post creation; called when user clicks Publish | Calls X API; returns `{status, externalId, externalUrl}` |

### Full Registration Pattern

```typescript
prepareContentPublisher({
  getPublishConfiguration,
  renderSettingsUi: ({ updatePublishSettings, registerOnSettingsUiContextChange }) => { ... },
  renderPreviewUi: ({ registerOnPreviewChange }) => { ... },
  publishContent,
});
```

---

## 3. Key Data Primitives

| Primitive | Description | Limit |
|---|---|---|
| **OutputType** | Defines the media format Canva should export (e.g., "X Feed Post" = JPG/PNG, <5MB, 1:1 or 16:9 aspect ratio) | Configurable per app |
| **PublishRef** | Opaque JSON string carrying all settings between steps (caption, account, schedule time) | Max **5 KB** |
| **PreviewMedia** | Real-time file previews shown in the preview iframe; updates dynamically as settings change | Starts as thumbnail; upgrades to full video on demand |
| **OutputMedia** | Final production-ready exported files from Canva; passed to `publishContent` | Matches OutputType specs |

---

## 4. Required OAuth Scope

```
canva:design:content:read
```

This is the **only required scope** for the Content Publisher intent. It grants read access to design content for export.

For X OAuth 2.0, the app additionally requires:
- `tweet.write` — create tweets
- `media.write` — upload media files
- `offline.access` — for refresh token persistence

---

## 5. Authentication Architecture

### Deferred Authentication (Required Pattern)

Canva's design guidelines **require** deferred authentication for Content Publisher apps:
- Users MUST be able to see and interact with the Settings UI and Preview **before** connecting their X account
- Only at publish time (or when required) should authentication be triggered
- The `validityState` field controls the Publish button visibility:
  - `"invalid_authentication_required"` → hides the Publish button (shows Connect prompt)
  - `"invalid_missing_required_fields"` → hides Publish button (required fields incomplete)
  - `"valid"` → enables the Publish button

### Auth Implementation

```typescript
import { auth } from "@canva/user";
const oauth = auth.initOauth();

// Check token
const tokenResponse = await oauth.getAccessToken({ scope });

// Trigger auth flow
const response = await oauth.requestAuthorization({ scope });
```

### Auth UX Rules (from Canva design guidelines)
- Auth flows happen in a **pop-up window**, NOT inside the app iframe
- Use `requestAuthentication` method — don't bypass the standard flow
- Show X account **handle** (not email) as the active account indicator
- Display account avatar/profile picture when available
- Support multi-account: allow users to switch between connected X accounts
- **Disconnection handling**: when a user removes the app, delete the OAuth token; require re-auth on reconnect

---

## 6. UI Architecture (Side Panel Anatomy)

The right-hand side panel is split into two areas:

### A. Canva-Managed UI (top — developer cannot modify)
- **Minified preview** — collapsed preview with expand toggle
- **Output type selector** — dropdown for format (e.g., "X Feed Post", "X Video Post")
- **Page selector** — pick which Canva pages/slides to export

### B. App Settings UI (bottom — developer-owned)
Must implement using **App UI Kit** components only. Recommended structure:
1. Account indicator (X handle + avatar)
2. Caption text area (character counter: 280 / 25,000 premium)
3. Scheduling toggle (date/time picker) — _must be implemented by the app, not Canva_
4. Any additional X-specific settings (e.g., reply permissions)

> ⚠️ **Do NOT add a custom "Publish" button** — the Publish button is managed entirely by Canva.

---

## 7. Preview UI Requirements

The Preview UI renders in a **separate iframe** from the Settings UI:
- Must update in **real time** as settings change
- Must handle all states: loading, ready, error
- For **images**: show placeholder while loading, smooth transition when ready
- For **videos**: show progress bar (NOT a spinner), start with thumbnail, upgrade to full video on demand
- Must reflect dark/light mode toggle
- Post-publish: provide a URL that links to the live X post (becomes the primary CTA in Canva's post-publish dialog)

---

## 8. ⚠️ Critical Constraint: Native Scheduling NOT Supported

> **"The Content Publisher intent doesn't support Canva native scheduling yet, so you need to provide a way for the user to input the timing to schedule on your platform."**
> — Canva Design Guidelines, Content Publisher

**Implication for the PRD:**
- The app **must implement its own scheduling backend** (server-side queue or X API-backed scheduling)
- The scheduling UI (date/time picker, timezone display) must be built in the **App Settings UI** section
- The preview should reflect the scheduled time if possible
- Always display the **timezone** explicitly alongside the date/time picker
- Default to the user's local timezone with ability to change

---

## 9. Error Handling Requirements

### In `publishContent`:
- Return `AppError` for unrecoverable errors (generic error message shown above Publish button)
- Return custom error message for recoverable errors (e.g., "Your X session has expired — reconnect your account")
- Provide custom Settings UI treatment for field-level errors

### Auth errors (minimum required):
- Server error message
- Catch-all fallback message
- Permission denied message
- Temporarily unavailable message

---

## 10. Canva Marketplace Submission Requirements

To publish on the Canva App Marketplace, the app must pass Canva's review. Key requirements:
- Must use **App UI Kit** components throughout
- Must implement all **4 Content Publisher intent functions** correctly
- Auth must follow the **deferred authentication** pattern
- Must support **light and dark mode**
- Must be **accessible** (WCAG compliant)
- Must be **responsive** (mobile/tablet support in Canva)
- Must be **internationalization-ready** (i18n)
- Post-publish URL must point to the live X post

---

## 11. Relevant API References

| API / Resource | URL |
|---|---|
| Content Publisher Intent | https://www.canva.dev/docs/apps/intents/content-publisher/ |
| Implementation Guide | https://www.canva.dev/docs/apps/intents/content-publisher/implementation-guide/ |
| App Template (CLI) | https://www.canva.dev/docs/apps/app-templates/content-publisher/ |
| Design Guidelines | https://www.canva.dev/docs/apps/design-guidelines/content-publisher/ |
| Auth Guidelines | https://www.canva.dev/docs/apps/design-guidelines/authentication/ |
| OAuth Integration | https://www.canva.dev/docs/apps/authenticating-users/oauth/ |
| App UI Kit | https://www.canva.dev/docs/apps/app-ui-kit/ |
| Scopes Reference | https://www.canva.dev/docs/apps/configuring-scopes/ |
| Submitting Apps | https://www.canva.dev/docs/apps/submitting-apps/ |
| GitHub Starter Kit | https://github.com/canva-sdks/canva-apps-sdk-starter-kit |

---

*Research compiled: 2026-03-24 | Source: canva.dev official documentation*
