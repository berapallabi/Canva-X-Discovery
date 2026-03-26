# Success Criteria

## User Success

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

## Business Success

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

## Technical Success

| Requirement | Target | Rationale |
|---|---|---|
| Publish success rate | ≥ 99% of attempts | Core trust signal |
| P95 publish latency | < 8 seconds | Below user perception threshold |
| X API error rate | < 1% of attempts | SLA for reliability |
| OAuth re-auth rate | < 5%/month | Stable token = stable trust |
| Media validation pass rate | ≥ 98% | Prevent silent upload failures |
| Preview render time | < 3 seconds | Real-time feedback expectation |

---

## Measurable Outcomes — Go/No-Go Gates

MVP is ready to scale when **all three gates** are met:

| Gate | Metric | Target |
|---|---|---|
| Activation Gate | Install → First Publish conversion | ≥ 15% / 7 days |
| Reliability Gate | Publish success rate | ≥ 99% of attempts |
| Speed Gate | Time-to-publish (North Star) | < 60 seconds median |

---
