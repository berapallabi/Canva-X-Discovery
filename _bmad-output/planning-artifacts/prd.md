---
stepsCompleted: [step-01-init, step-02-discovery, step-02b-vision, step-02c-executive-summary, step-03-success, step-04-journeys, step-05-domain, step-06-innovation, step-07-project-type, step-08-scoping, step-09-functional, step-10-nonfunctional, step-11-polish, step-12-complete]
inputDocuments:
  - "_bmad-output/planning-artifacts/product-brief-Canva-X-Discovery-2026-03-23.md"
  - "_bmad-output/planning-artifacts/research/domain-canva-x-publishing-integration-research-2026-03-23.md"
  - "_bmad-output/planning-artifacts/research/canva-sdk-content-publisher-technical-research-2026-03-24.md"
workflowType: 'prd'
briefCount: 1
researchCount: 2
brainstormingCount: 0
projectDocsCount: 0
classification:
  projectType: "platform_extension"
  domain: "social_media_publishing"
  complexity: "medium"
  projectContext: "greenfield"
  platformDependency: "canva_apps_sdk"
  externalIntegration: "x_api_v2"
---

# Product Requirements Document — Canva X Publisher

**Author:** Pallabi
**Date:** 2026-03-24
**Status:** Draft v1.0

---

## Executive Summary

**Canva X Publisher** is a native Canva Marketplace App built on the Content Publisher intent (Canva Apps SDK), enabling Canva users to publish designs, images, and videos directly to X (formerly Twitter) from within the Canva editor — without downloading, switching tabs, or manually uploading files.

**Target Users:**
- **Social Media Managers (SMMs):** Agency and in-house professionals managing multiple X accounts, for whom file-based workflows create compounding organisational debt.
- **Solo Content Entrepreneurs:** Batch-create weekly visual content in Canva; need to schedule and publish daily without leaving the editor.
- **Real-Time Marketers:** React to trending moments; require instant, zero-friction "Post Now" capability.

**Core Problem:** The "Context-Switching Tax" — every design-to-X workflow today forces the creator to: export → navigate to X → upload → caption → post. If a typo or brand error is caught at upload time, the full cycle restarts. For time-sensitive posting (trend response, campaign launches), this latency is a direct competitive disadvantage.

**Proposed Solution:** A Content Publisher intent app registered on the Canva Marketplace that eliminates the download step entirely. The app renders a Settings UI (caption, account selector, schedule picker) and a real-time WYSIWYG X feed preview within Canva's right-hand side panel. When the user clicks Publish, the app calls `publishContent()`, uploads the exported media to X via API v2 chunked upload, and creates the tweet — all without the user leaving the Canva editor.

**North Star Metric:** Time from design-complete to post-live on X < 60 seconds.

---

### What Makes This Special

1. **Zero File System:** No downloads, no folders, no `_final_v2.png`. The Canva design is the permanent source of truth. The `prepareContentPublisher` SDK handles all export and media delivery internally via `OutputMedia`.

2. **Context-Native, Not Bolted On:** Built using Canva's official Content Publisher intent — the app lives inside Canva's editor panel, using App UI Kit components, indistinguishable from a native Canva feature.

3. **X-Optimised by Design:** Real-time feed preview (light/dark mode), character counter (280 standard / 25,000 X Premium), X media validation pre-flight (format, size, duration), per-user OAuth 2.0 tokens.

4. **Deferred Auth, Zero Friction:** Users can configure settings and preview their post before connecting their X account — consistent with Canva's documented deferred authentication pattern (`validityState: "invalid_authentication_required"`).

5. **Scheduling is App-Owned:** Since Canva's Content Publisher intent does not yet support native scheduling, the app implements a server-side scheduling queue. This is a technical differentiator — no competitor offers in-editor scheduling via the Canva Marketplace at this fidelity.

---

## Project Classification

| Attribute | Value |
|---|---|
| **Project Type** | Canva Marketplace App (Content Publisher intent — Canva Apps SDK) |
| **Domain** | Social Media Publishing |
| **Complexity** | Medium — dual-API dependency (Canva SDK + X API v2), custom scheduling backend, OAuth lifecycle management, media validation |
| **Project Context** | Greenfield |
| **Platform Dependency** | Canva Apps SDK (`prepareContentPublisher`, `@canva/user`, App UI Kit) |
| **External Integration** | X API v2 (`POST /2/tweets`, `POST /2/media/upload`) |
| **Required Scope** | `canva:design:content:read` + X OAuth 2.0 (`tweet.write`, `media.write`, `offline.access`) |

---

## Success Criteria

### User Success

**Activation:** First successful "Post Now" from the Canva side panel (OAuth confirmed, media uploaded, tweet live on X). This is the single most important lifecycle event — it confirms the bridge works end-to-end.
- Activation target: ≥ 15% of app installs complete first Post Now within 7 days.

**Retention — Power User Signal:** A user who publishes or schedules 3+ posts/week via the extension. This indicates the app has replaced the manual workflow, not supplemented it.

User lifecycle tiers:
- **Activated:** 1 successful Post Now
- **Engaged:** 1–2 posts/week
- **Power User (retained):** 3+ posts/week
- **At-Risk (churned):** No publish in 14 days

**The "Wow" Threshold:** First batch schedule of 3+ posts in a single session — the user's week is "queued and cleared" without leaving Canva.
- Target: ≥ 25% of retained users complete at least one batch schedule session/month.

---

### Business Success

**Revenue Model:**
- **Monetisation:** The integration will be offered as a free utility to drive Canva user acquisition, retention, and platform stickiness.

**Growth Targets (3 months post-launch):**

| Metric | Target |
|---|---|
| Monthly Active Users (MAU) | 5,000 |
| Install → First Publish conversion | ≥ 15% (7-day rolling window) |
| Canva Marketplace rating | ≥ 4.5 / 5.0 |

**Anti-Metrics (explicitly not optimised for):**
- **Total post volume per user** — incentivises spam; tracks volume, not value
- **Time spent in panel** — if the Speed Job works, dwell time should be low
- **DAU** — WAU and Power User rate are the correct lenses for batch-publish creators
- **Feature breadth usage** — success is publish completion, not feature exploration

---

### Technical Success

| Requirement | Target | Rationale |
|---|---|---|
| Publish success rate | ≥ 99% of attempts | Core trust signal |
| P95 publish latency | < 8 seconds | Below user perception threshold |
| X API error rate | < 1% of attempts | SLA for reliability |
| OAuth re-auth rate | < 5%/month | Stable token = stable trust |
| Media validation pass rate | ≥ 98% | Prevent silent upload failures |
| Preview render time | < 3 seconds | Real-time feedback expectation |

---

### Measurable Outcomes — Go/No-Go Gates

MVP is ready to scale when **all three gates** are met:

| Gate | Metric | Target |
|---|---|---|
| Activation Gate | Install → First Publish conversion | ≥ 15% / 7 days |
| Reliability Gate | Publish success rate | ≥ 99% of attempts |
| Speed Gate | Time-to-publish (North Star) | < 60 seconds median |

---

## Product Scope

### MVP — Minimum Viable Product

| # | Feature | Priority |
|---|---|---|
| 1 | Post Now (instant publish via `publishContent()`) | Must-Have |
| 2 | X OAuth 2.0 Connect (deferred auth, `auth.initOauth()`) | Must-Have |
| 3 | Multi-account support (account selector in Settings UI) | Must-Have |
| 4 | WYSIWYG X feed preview (`renderPreviewUi`, light/dark mode) | Must-Have |
| 5 | X media pre-flight validation (format, size, duration) | Must-Have |
| 6 | Caption editor (character counter: 280 / 25K Premium) | Must-Have |
| 9 | Post-publish confirmation + live tweet URL | Must-Have |
| 10 | OAuth token persistence (no re-login per session) | Must-Have |

### Growth Features (Post-MVP)

- AI caption generator (LLM-powered, tuned for X voice and trending topics)
- Batch scheduling (queue up to 10 posts in a single session)
- Thread publisher & Reply workflows (Quote tweets, replies, and threads are strictly out-of-scope for MVP)
- Team approval workflow (SMM submit → brand manager approve → publish)

### Expansion Vision (12–24 months)

- Multi-platform publishing (LinkedIn, Instagram, Facebook)
- Post-publish analytics (pull X engagement data back into Canva)
- Content intelligence (recommend post times based on past performance)
- "Creative-to-feed OS" — Canva as the unified creation and distribution hub

---

## User Journeys

### Journey 1: Alex — Social Media Manager ("Speed Mode")

**Persona:** Alex manages social content for 3 client brands at a mid-size agency. Every Friday is "content day" — bulk-creating posts in Canva for the following week. Monday mornings are chaos: a Slack full of approvals and a folder of files to manually upload across platforms.

**Opening Scene:** 9:10am Monday. Alex has 6 approved graphics in Canva and a trending hashtag that expires in 2 hours. They open the Canva X Publisher panel — inside the editor.

**Journey:**
1. Alex selects the first design — the panel pre-populates with Canva's exported output.
2. They tap the account selector, switch from Brand A to Brand B (@brandB_official) — visible at a glance with avatar.
3. They type a 140-character caption, see the real-time X preview update (dark mode, brand colours, profile picture visible).
4. Click "Post Now" — Canva confirms: *"Live on X → View Post →"*

**Value Moment:** Total time from opening panel to live tweet: 38 seconds. No files touched, no new tabs opened. Alex completes all 6 posts in 4 minutes.

**Capabilities Revealed:** Multi-account selector, Post Now, real-time preview, post-publish URL, token persistence.

---

### Journey 2: Priya — Solo Creator ("Planning Mode")

**Persona:** Priya runs a career-coaching newsletter and X account. She batch-creates content every Sunday for the full week — 7 posts, one per day. Current workflow: Canva download → Hootsuite upload → schedule. Three apps, 45 minutes.

**Opening Scene:** Sunday 7pm. Priya finishes her 7 designs and opens the Canva X Publisher panel.

**Journey:**
1. Priya clicks the first design, writes the caption, picks Monday 9am IST — timezone confirmed.
2. The preview updates: X card renders correctly. She resizes the image in Canva; preview updates instantly.
3. She schedules all 7 posts across the week — each ~3 minutes.
4. A "Queue" view confirms 7 scheduled posts with dates and times.

**Value Moment:** Week's content scheduled in 21 minutes entirely inside Canva. She closes the laptop before 8pm — "queued and cleared." Cancels Hootsuite within 2 weeks.

**Capabilities Revealed:** Scheduling with timezone display, server-side queue, live preview sync, queue view/management.

---

### Journey 3: Marco — Real-Time Marketer ("Trend Response Mode")

**Persona:** Marco manages community for a SaaS startup. He monitors X trends throughout the day; when a relevant conversation spikes, he needs a reactive graphic live within 90 seconds to capture impressions.

**Opening Scene:** 2:47pm. A product meme goes viral that fits Marco's brand. He opens a Canva template, edits copy in 45 seconds, hits the publish panel.

**Journey:**
1. Panel opens — last-used X account pre-selected (no re-auth needed, token persisted).
2. Marco types: "This is us 😂 [trending hashtag]" — 7 words.
3. Preview renders. Crop looks right.
4. "Post Now" → live in 4 seconds.

**Value Moment:** From trend-spotted to tweet live: 63 seconds. North Star achieved.

**Capabilities Revealed:** Persistent OAuth token, last-used account pre-selected, fast-path minimal-friction publish flow.

---

### Journey 4: Admin / Ops — Scheduled Post Failure Recovery

**Persona:** The backend operator monitoring the scheduling queue for errors and X API rate-limit events.

**Opening Scene:** Scheduling service fires at 9:00am for Priya's Monday post. X API returns 429 (rate limit exceeded).

**Journey:**
1. System marks the post `retry_pending`.
2. Exponential backoff retries: +5min, +15min, +30min.
3. After 3 failed retries, post is marked `failed`.
4. Next time Priya opens the app: in-panel notification — "Your Monday 9am post failed to publish. Retry?"

**Capabilities Revealed:** Scheduling queue with retry/backoff, failed delivery notifications, manual retry capability, ops logging.

---

### Journey Requirements Summary

| Journey | Capabilities Required |
|---|---|
| Alex (Speed) | Multi-account, Post Now, real-time preview, post-publish URL |
| Priya (Planning) | Scheduling, timezone picker, queue management, preview sync |
| Marco (Trend) | Token persistence, fast-path publish |
| Admin (Ops) | Retry logic, failure notifications, queue observability |

---

## Domain-Specific Requirements

### Platform Policy Constraints

- **Canva Marketplace Review:** App must pass Canva's submission review — Content Publisher intent correctly implemented, App UI Kit components only, accessible, internationalisation-ready, light/dark mode support, deferred auth pattern strictly followed.
- **X Developer Policy:** Explicit user-intent required for each publish action. Automated or bulk-API posting patterns prohibited. Tweet content may not be stored beyond the publishing workflow.
- **X API Access Tier:** Free API tier allows 500,000 tweets/month. Per-user OAuth distributes quota across accounts. Pro API tier required at > 500K monthly tweet volume.

### Technical Constraints

- **PublishRef Size Limit:** Canva SDK `PublishRef` payload capped at **5KB**. Caption + account ID + schedule timestamp must fit within this limit.
- **Media Specifications — X Feed Post:**
  - Images: JPG/PNG/WEBP, max 5MB, aspect ratio 16:9 or 1:1
  - Videos: MP4/MOV, max 512MB, max 2:20 duration, min 1280×720 resolution
- **OAuth Token Storage:** Access + refresh tokens must be stored server-side (encrypted at rest). Never in browser, frontend state, or Canva app storage.
- **Scheduling Infrastructure:** Requires a persistent backend service with job queue. Canva's Content Publisher intent provides no scheduling primitives.

### Risk Mitigations

| Risk | Mitigation |
|---|---|
| X API rate limits (429) | Per-user OAuth distributes quota; exponential backoff retry for schedule queue failures |
| Canva review rejection | Strictly follow Content Publisher intent design guidelines; no App UI Kit bypass |
| X platform policy violations | User-initiated publish only; no automated/bulk-API patterns |
| OAuth token expiry at scheduled delivery | Refresh token flow triggered on schedule execution; user notified if refresh fails |

---

## Innovation & Novel Patterns

### Detected Innovation Areas

**1. In-Editor, Zero-Download Publishing Pipeline**
No existing Canva Marketplace app delivers production-quality X publishing via the Content Publisher intent. All existing workarounds (Buffer, Hootsuite, Canva's social scheduler) require the user to leave the editor. This is the first integration that eliminates the filesystem entirely.

**2. App-Owned Scheduling Inside Canva**
Canva's Content Publisher intent intentionally omits scheduling. Building scheduling correctly — server-side queue, timezone display, retry logic, failure notifications — within the Canva panel is a novel engineering challenge with no marketplace precedent.

**3. Real-Time WYSIWYG X Feed Preview**
Using `PreviewMedia` + `registerOnPreviewChange()` to render a live, accurate X feed preview (light/dark mode, character count) that dynamically updates as the user edits the design — without leaving the editor — is a differentiating UX experience not available from competing apps.

### Validation Approach

| Innovation | Validation Method |
|---|---|
| Zero-download pipeline | Time-to-publish benchmark: < 60s median (vs. ~5min manual workflow) |
| In-editor scheduling | % users cancelling third-party scheduling tools at 60 days post-install (survey) |
| Real-time preview accuracy | % of previews matching live post appearance (automated screenshot comparison) |

---

## Platform Extension Specific Requirements

### Technical Architecture

```
[Canva Editor — Content Publisher Side Panel (SDK iframe)]
    ├── Settings UI (renderSettingsUi)
    │     ├── X Account Selector (handle + avatar)
    │     ├── Caption Editor (character counter)
    │     ├── Schedule Picker (date/time/timezone)
    │     └── OAuth Connect button (deferred auth)
    ├── Preview UI (renderPreviewUi)
    │     └── WYSIWYG X Feed Simulation (light/dark)
    └── publishContent()
            ├── Receive OutputMedia (Canva-exported files)
            ├── Upload to X via POST /2/media/upload (chunked)
            └── Create tweet via POST /2/tweets

[Backend Service]
    ├── OAuth Token Manager (store, refresh, revoke)
    ├── Scheduling Queue (job scheduler + retry logic)
    └── X API Proxy (media upload, tweet creation)
```

### SDK Integration Reference

| SDK Component | Usage |
|---|---|
| `prepareContentPublisher()` | Entry point — registers all 4 intent functions |
| `getPublishConfiguration` | Defines OutputType (X Feed Image/Video specs) |
| `renderSettingsUi` | Caption, account selector, schedule picker |
| `renderPreviewUi` | Live X feed preview iframe |
| `publishContent` | Receives OutputMedia, calls X API, returns post URL |
| `auth.initOauth()` | X OAuth 2.0 flow setup |
| `oauth.getAccessToken()` | Token retrieval on settings load |
| `oauth.requestAuthorization()` | OAuth pop-up trigger |
| `validityState` | Controls Publish button visibility |

### Output Type Configuration

```
X Feed Post (Image):
  - Format: JPG / PNG / WEBP
  - Max Size: 5MB
  - Aspect Ratio: 1:1 or 16:9 or 4:5

X Feed Post (Video):
  - Format: MP4 / MOV
  - Max Size: 512MB
  - Max Duration: 2:20 (140 seconds)
  - Min Resolution: 1280×720
```

### Authentication Model

- **Deferred Auth:** Users can enter caption and preview before connecting X account
- **OAuth 2.0 PKCE:** Initiated via `auth.initOauth()` inside the intent
- **Token Storage:** Server-side only, encrypted at rest — never in frontend
- **Multi-Account:** Users can connect multiple X accounts; selector shows handle + avatar
- **Disconnection:** App removal triggers server-side token deletion
- **Per-Team Auth:** Each Canva team requires independent X authentication

### Implementation Notes

- App scaffolded with `npx @canva/cli create --template content-publisher`
- All UI via `@canva/app-ui-kit` — no third-party component libraries
- Must support Canva light and dark mode
- Must be internationalisation-ready (all strings externalised)
- Responsive across desktop and tablet surfaces
- Post-publish response must include `externalUrl` linking to live X post

---

## Functional Requirements

### Account & Authentication Management

- **FR1:** Users can connect their X account to the app via OAuth 2.0 without leaving the Canva editor.
- **FR2:** Users can view which X account is currently active (handle + avatar displayed in Settings UI).
- **FR3:** Users can connect multiple X accounts and switch between them from the account selector.
- **FR4:** Users can disconnect their X account, which triggers immediate server-side token deletion.
- **FR5:** The app preserves the user's X authentication session across Canva editor sessions. The backend must securely map the stored X OAuth tokens to the user's verified Canva User ID.
- **FR6:** Users can interact with the Settings UI and preview their post before connecting their X account (deferred authentication). To build trust, the UI must explain the required X permissions (*offline.access, tweet.write*) immediately above the "Connect X" button prior to launching the OAuth pop-up.

### Content Configuration

- **FR7:** Users can write and edit a post caption within the Settings UI.
- **FR8:** Users can view a live character count for their caption (280-character standard; 25,000-character X Premium).
- **FR9:** Users can select which Canva design pages to include in their post (managed by Canva's page selector component). In the MVP, multiple selected pages are attached as multiple images to a single tweet (up to X's limit of 4 images per tweet).
- **FR10:** Users can select the output type for their post (X Feed Image Post or X Feed Video Post) from Canva's output type dropdown.
- **FR11:** The app validates media against X's technical specifications before publishing (format, file size, aspect ratio, video duration and resolution). The frontend must also validate that the total string payload (caption + metadata) fits within the strict 5KB Canva SDK `PublishRef` limit.
- **FR12:** Users receive clear, actionable error messages when media or payload validation fails, with specific guidance on how to resolve the issue.

### Preview

- **FR13:** Users can view a real-time WYSIWYG preview of their post as it will appear in the X feed, explicitly simulating X's specific center-cropping logic for irregular aspect ratios.
- **FR14:** The preview updates dynamically as the user edits their caption or modifies the Canva design, correctly scaling and cropping.
- **FR15:** The preview renders in both X light mode and dark mode.
- **FR16:** The preview displays the active X account handle and avatar.
- **FR17:** The preview shows appropriate loading states — placeholder image for images; progress bar for videos.

### Instant Publishing

- **FR18:** Users can publish their Canva design directly to X as a tweet without downloading any files.
- **FR19:** Users receive a publish confirmation with a direct URL link to the live tweet on X.
- **FR20:** The app returns the live tweet URL, which Canva displays as the primary action in the post-publish success dialog.
- **FR21:** Users can initiate X account authentication from within the publish flow if not previously connected.

### Error Handling & Reliability

- **FR30:** The app surfaces X API errors to the user with clear, platform-specific error messages (e.g., session expired, duplicate content, media rejected).
- **FR31:** The stateless proxy implements transient retry logic for immediate X API failures (429 rate limit, 503 server errors).

---

## Non-Functional Requirements

### Performance

- **NFR1:** The Settings UI must load and be interactive within 2 seconds of opening the side panel.
- **NFR2:** The X feed preview must render within 3 seconds of panel open.
- **NFR3:** "Post Now" must complete — from user click to tweet live — within 8 seconds at P95.
- **NFR4:** The full publish pipeline must achieve P50 latency of < 60 seconds (aligned with North Star metric).
- **NFR5:** Preview must update within 1 second of caption change (real-time feedback expectation).

### Security

- **NFR6:** X OAuth access tokens and refresh tokens must be stored server-side only, encrypted at rest (AES-256 or equivalent). Never stored in browser, frontend state, or Canva app storage.
- **NFR7:** All communication between the Canva app frontend and the backend must occur over HTTPS/TLS 1.2+.
- **NFR8:** The OAuth pop-up must follow Canva's pop-up guidelines — no auth forms displayed inside the app iframe.
- **NFR9:** Token revocation must be immediate upon user-initiated disconnect or app removal.
- **NFR10:** The backend must cryptographically verify the Canva-signed JWT (`Authorization` header) on every custom API request to securely identify the Canva User ID before interacting with their X tokens.

### Scalability

- **NFR11:** Next.js serverless architecture scales automatically to handle traffic spikes.
- **NFR12:** The app backend must scale to 10x user growth (50,000 MAU) without architectural changes — achieved via explicitly stateless API routing.
- **NFR13:** X API quota must be managed at the per-user level, preventing any single user's volume from affecting other users' availability.

### Accessibility & Platform Compliance

- **NFR14:** The Settings UI and Preview UI must meet WCAG 2.1 AA accessibility standards (required by Canva's app submission guidelines).
- **NFR15:** The app must support both Canva light mode and dark mode without visual regressions.
- **NFR16:** The app must be internationalisation-ready — all strings externalised, ready for translation (required for Canva Marketplace listing).
- **NFR17:** The app must be responsive across Canva's desktop (primary) and tablet (secondary) surfaces.

### Integration Reliability

- **NFR18:** The codebase must remain thoroughly stateless ensuring idempotent API deliveries.
- **NFR19:** Video media upload must use X's chunked upload endpoint (`/2/media/upload` with `INIT`/`APPEND`/`FINALIZE` commands).
- **NFR21:** The app must handle OAuth token expiry gracefully using Canva's getAccessToken method.
