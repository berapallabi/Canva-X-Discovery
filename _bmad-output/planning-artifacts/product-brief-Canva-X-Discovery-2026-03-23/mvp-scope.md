# MVP Scope

## Core Features (In Scope — v1)

| # | Feature | Description | Tier | Priority |
|---|---|---|---|---|
| 1 | **Post Now (Instant Publish)** | One-click export of current Canva page + push live to X via API v2. No download, no browser switch. | Free | 🔴 Must-Have |
| 2 | **X OAuth Connect** | Secure OAuth 2.0 flow to connect one or more X accounts. Persistent token management with auto-refresh. | Free | 🔴 Must-Have |
| 3 | **Multi-Account Support** | Connect and switch between multiple X accounts (agency/SMM use case). Account selector in side panel. | Free | 🔴 Must-Have |
| 4 | **WYSIWYG X Feed Preview** | Real-time preview of how the design renders in the X feed. Light/dark mode toggle. Crop & text safety zone indicators. | Free | 🔴 Must-Have |
| 5 | **X Media Validation** | Pre-publish validation of format (JPG/PNG/MP4/GIF), size (<5MB image, <512MB video), and duration (<2m20s standard). Clear user-facing error messages. | Free | 🔴 Must-Have |
| 6 | **Caption Editor** | In-panel text area for the post body. Real-time character counter (280 standard / 25,000 X Premium). | Free | 🔴 Must-Have |
| 7 | **Scheduling (Date & Time Picker)** | Set a future publish date and time. X API-backed or server-side queue. | Pro | 🟠 High |

> **Freemium Split:** Features 1–6 are **Free** (drive activation). Feature 7 (Scheduling) is **Pro-only** — the natural paywall trigger when users want to plan content ahead without leaving Canva.

---

## Out of Scope for MVP (v2+)

| Feature | Reason Deferred | Target Version |
|---|---|---|
| **Thread Publishing** | Requires multi-post sequencing logic, significant UX complexity, and separate X API calls per thread item | v2 |
| **Batch Scheduling** | Queue manager UI and backend scheduling infrastructure — validate single scheduling first | v2 |
| **Post-Publish Analytics** | Requires X analytics API (separate OAuth scope), data storage, and dashboard UI | v2 |
| **AI Caption Generator** | Valuable but not core to the Speed Job; dependent on LLM integration layer | v2/v3 |
| **Multi-Platform Publishing** | LinkedIn, Instagram, Facebook — powerful future play, dilutes v1 X excellence focus | v3 |
| **Team Approval Workflows** | Brand manager sign-off before posting — important for enterprise, out of SMM/solo creator v1 scope | v2 |

---

## MVP Success Criteria (Go/No-Go Gates)

The MVP is considered successful and ready to scale when **all three gates** are met:

| Gate | Signal | Target |
|---|---|---|
| **Activation Gate** | Install → First Publish conversion rate | ≥ 15% within 7 days of install |
| **Reliability Gate** | Publish success rate (no X API errors) | ≥ 99% of all attempts |
| **Speed Gate** | Median time-to-publish (North Star metric) | < 60 seconds end-to-end |

When all three gates are green:
1. Unlock and promote the **Pro Scheduling** tier
2. Begin v2 discovery (Thread Publishing + Batch Scheduling)
3. Scale Canva Marketplace marketing & growth initiatives

---

## Future Vision (v2–v3 Roadmap)

If Canva X Publisher succeeds in v1, it grows into an **in-Canva content intelligence layer** — not just a publisher, but a creative-to-distribution operating system.

### v2 — Power Creator Suite
- **Thread Publisher**: Convert a multi-page Canva deck into an X thread in one click. Each page becomes one tweet.
- **Batch Scheduler**: A full content queue manager — load 10 posts, set a cadence, let the system handle execution.
- **AI Caption Generator**: LLM-powered caption suggestions tuned for X's voice — tone, hashtags, hooks, generated from the design's visual content.
- **Team Approval Workflows**: Brand manager sign-off before publish — SMM submits, brand approves, all within Canva.

### v3 — Multi-Platform Command Centre
- **Multi-Platform Publishing**: Extend the panel to LinkedIn, Instagram, and Facebook — one design, one click, multiple destinations.
- **Post-Publish Analytics**: Pull X engagement data (impressions, likes, reposts, link clicks) back into Canva. Close the creative feedback loop.
- **Content Intelligence**: Recommend optimal post times, best-performing design styles, and caption patterns — based on the user's own X performance history.

### Long-Term North Star
> *Canva X Publisher becomes the* ***"creative-to-feed operating system"*** *— where every design decision is informed by what performed well, and every publish action is one click from the canvas.*

