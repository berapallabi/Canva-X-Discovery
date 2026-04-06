# Product Scope

## MVP — Minimum Viable Product

| # | Feature | Priority |
|---|---|---|
| 1 | Post Now (instant publish via `publishContent()`) | Must-Have |
| 2 | X OAuth 2.0 Connect (deferred auth, `auth.initOauth()`) | Must-Have |
| 3 | Multi-account support (account selector in Settings UI) | Must-Have |
| 4 | WYSIWYG X feed preview (Real-time dynamic overlay) | Must-Have |
| 5 | X media pre-flight validation (format, size, chunking) | Must-Have |
| 6 | Exact X-Native Parity (Alt-Text, Reply Controls, Content Warnings) | Must-Have |
| 7 | Supported Media Formats: Static Image (PNG/JPG) & Video (MP4) | Must-Have |
| 8 | Post-publish confirmation + live tweet URL | Must-Have |
| 9 | OAuth token persistence & explicit native revocation | Must-Have |

## MVP External API Blockers (Degraded Features)

Recent technical spikes confirmed that X API v2 actively blocks the following originally proposed MVP features. They remain in the UI but with forced graceful degradation:
1. **User Media Tagging:** Degraded. Will append standard `@handles` to the text caption.
2. **Local Sensitive Content Flags:** Degraded. Will apply a visual blur inside Canva Preview only; the live post relies on X account-level defaults.

## Parking Lot / Deferred Features

Based on technical feasibility and scoped simplifications during UX design, the following features have been explicitly removed from the MVP and parked for future iterations:
- **Threaded Posts (Multi-Tweet Threads):** Out of scope. Users can only publish single fixed-format posts.
- **GIF & Poll Formats:** Out of scope. The MVP exclusively supports Images and Videos.
- **Premium Character Limits (25,000 chars):** Out of scope. All posts will enforce a strict 250-character limit regardless of user subscription tier.
- **Custom Post Types:** The output uses a "Fixed format type - Post", dropping any separate Output Type dropdown.

## Strictly Out of Scope (Architectural Constraints)

Because we have elected for a **Stateless Proxy / Zero Database Architecture** to maximize security and minimize maintenance, any feature requiring persistent background state is strictly impossible for this integration.
- **Batch Scheduling:** Impossible without a database to hold the scheduled payload and a cron worker.
- **Automated Retweeting/Recurring Posts:** Impossible without a database queue.

## Expansion Vision (12–24 months)

- Multi-platform publishing (LinkedIn, Instagram, Facebook) introducing a stateful persistence layer.
- Post-publish analytics (pull X engagement data back into Canva)
- Content intelligence (recommend post times based on past performance)
- "Creative-to-feed OS" — Canva as the unified creation and distribution hub
