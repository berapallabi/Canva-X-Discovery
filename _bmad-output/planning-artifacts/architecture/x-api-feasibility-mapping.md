# X API Technical Feasibility & Endpoint Mapping

This document serves as the architectural spike validating the functional features outlined in the PRD against the strict, public limitations of the X (Twitter) API infrastructure (v2 and v1.1).

## 1. Prerequisites: X Developer Portal Configuration

Before a single line of code is written, the underlying X Developer App must be configured with exact parameters to permit the Canva integration.

| Configuration | Required Value | Rationale |
| :--- | :--- | :--- |
| **App Type** | Web App / Confidential Client | Allows the Next.js proxy to securely exchange authorization codes. |
| **Authentication** | `OAuth 2.0` | Mandatory for modern X API v2 interactions. |
| **Callback URIs** | `https://api.canva.com/...` | The exact Canva Marketplace redirect URI must be explicitly whitelisted in the X Portal. |
| **OAuth 2.0 Scopes** | `tweet.read`, `tweet.write`, `users.read`, `offline.access` | `tweet.write` is required to publish. `offline.access` is absolutely critical to receive a `refresh_token`; without it, the Canva integration would force the user to re-login every 2 hours. |
| **API Tier** | **Pro Tier or Higher** | *Critical Warning:* The X "Free" and "Basic" tiers heavily restrict App-Level limits (Basic caps at 3,000 tweets/month globally). For a marketplace-wide Canva app, an Enterprise scalable tier is functionally mandatory to avoid daily global 429 Rate Limit crashes. |

---

## 2. Feature-to-Endpoint Mapping

This table maps every user-facing PRD interaction to the explicit X API Endpoint and Payload required to fulfill it.

| PRD Feature | X API Endpoint | Version | Method | Implementation Details & Validation Schema |
| :--- | :--- | :--- | :--- | :--- |
| **Fetch Profile** (Avatar, @handle) | `/2/users/me` | v2 | `GET` | **Query Params:** `?user.fields=profile_image_url`. Returns the active user context for the Canva UI Account Selector. |
| **Upload Image** (<5MB) | `upload.twitter.com/1.1/media/upload.json` | v1.1 | `POST` | X API v2 *does not* support media uploads. The app must fallback to the v1.1 endpoint. Payload is standard `multipart/form-data`. Returns `media_id_string`. |
| **Upload Video** (>5MB) | `upload.twitter.com/1.1/media/upload.json` | v1.1 | `POST`| Requires 3-step sequence to bypass Vercel limits:<br>1. `command=INIT`<br>2. `command=APPEND` (Loop sending 3.9MB chunks)<br>3. `command=FINALIZE` |
| **Alt-Text / Video Caption (.srt) Processing** | `upload.twitter.com/1.1/media/metadata/create.json` | v1.1 | `POST` | Must be called successfully *after* `FINALIZE` but *before* the final post is published. Payload connects metadata payloads contextually. |
| **Publish Standard Post** | `/2/tweets` | v2 | `POST` | Core execution. Payload accepts `text` (Caption max 250 characters) and `media.media_ids`. |
| **Reply Controls** | `/2/tweets` | v2 | `POST` | Passed in the publish payload via `reply_settings`. Valid enum values: `mentionedUsers` or `following`. |
| **[PARKED] Thread Building** | `/2/tweets` | v2 | `POST` | Threading sequence flows have been parked and removed natively from MVP scope logic. |

---

## 3. Discovered API Limitations & Red Flags (Spike Outcomes)

During this API feasibility spike, we have identified features requested in the UX flow that are **actively unsupported** by the modern X API v2 payload schemas. The Product Manager must review these forced regressions.

### 🔴 Limitation 1: Image User Tagging
- **Proposed UX:** User adds "@handles" natively to the Canva image via an Advanced Setting.
- **API Reality:** The X /v2/tweets endpoint *does not support* the `media.tagged_users` parameter that previously existed in v1.1. It is technically impossible to attach user tags to visual media via the public v2 API today.
- **Forced Resolution:** Supported via post-style fallback where standard `@mentions` append physically to the end of the text Payload natively (max 10).

### 🔴 Limitation 2: Sensitive Content Warning Flags
- **Proposed UX:** User checks a box to "Flag media as sensitive" natively in Canva.
- **API Reality:** The X v2 payload schema completely dropped the `possibly_sensitive` boolean override. Sensitive media flags must now be configured statically at the X account level, not dynamically per-tweet via third-party APIs natively.
- **Forced Resolution:** The Canvia Settings UI includes checkboxes that only blur the visual Preview rendering inside Canva implicitly but the X post relies strictly on their profile policy natively.

### 🟡 Limitation 3: [PARKED] Premium Character Limits Verification
- **Proposed UX:** If user switches to an X Premium account, the Canva app magically allows 25,000 characters.
- **API Reality:** The `/2/users/me` endpoint *does not* return a boolean flag indicating if a user is subscribed to X Premium. The Canva proxy has no programmatic way of knowing natively.
- **Forced Resolution (Now Parked):** Our MVP officially resolves this by overriding everything natively capping rigidly at 250 characters strictly across the board for all connected profiles.
