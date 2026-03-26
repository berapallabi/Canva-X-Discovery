# Product Scope

## MVP — Minimum Viable Product

| # | Feature | Priority |
|---|---|---|
| 1 | Post Now (instant publish via `publishContent()`) | Must-Have |
| 2 | X OAuth 2.0 Connect (deferred auth, `auth.initOauth()`) | Must-Have |
| 3 | Multi-account support (account selector in Settings UI) | Must-Have |
| 4 | WYSIWYG X feed preview (Real-time dynamic overlay) | Must-Have |
| 5 | X media pre-flight validation (format, size, chunking) | Must-Have |
| 6 | Exact X-Native Feature Parity (Alt-Text, Threads, Reply Controls, Content Warnings) | Must-Have |
| 7 | Post-publish confirmation + live tweet URL | Must-Have |
| 8 | OAuth token persistence & explicit native revocation | Must-Have |

## Strictly Out of Scope (Architectural Constraints)

Because we have elected for a **Stateless Proxy / Zero Database Architecture** to maximize security and minimize maintenance, any feature requiring persistent background state is strictly impossible for this integration.
- **Batch Scheduling:** Impossible without a database to hold the scheduled payload and a cron worker.
- **Automated Retweeting/Recurring Posts:** Impossible without a database queue.

## Expansion Vision (12–24 months)

- Multi-platform publishing (LinkedIn, Instagram, Facebook) introducing a stateful persistence layer.
- Post-publish analytics (pull X engagement data back into Canva)
- Content intelligence (recommend post times based on past performance)
- "Creative-to-feed OS" — Canva as the unified creation and distribution hub

---
