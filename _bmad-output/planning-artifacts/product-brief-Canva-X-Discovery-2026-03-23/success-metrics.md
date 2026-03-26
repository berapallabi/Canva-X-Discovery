# Success Metrics

## North Star Metric

> **"Time from design-complete to post-live on X"** — Target: **< 60 seconds**
>
> Everything else is in service of this. If this number is high, something in the product is broken.

---

## User Success Metrics

### Activation (The Bridge is Live)

**Activation Event:** First successful **"Post Now"** from the Canva side panel.

This is the single most important moment in the user lifecycle. It confirms:
- OAuth connection to X is established and working
- The Canva Publish Extension integration is functional end-to-end
- The user has crossed from "installed" to "activated"

**Activation Target:** 15% of app installs complete their first "Post Now" within 7 days of installation.

---

### Retention (The Workflow Replacement Signal)

**Power User Definition:** A user who publishes or schedules **3+ posts per week** via the extension.

This is the signal that the app has **replaced** the manual download-upload workflow — not supplemented it. A user at this cadence has fundamentally changed how they publish to X.

| User Tier | Behaviour | Label |
|---|---|---|
| Activated | 1 successful Post Now | Activated |
| Engaged | 1–2 posts/week | Casual User |
| Retained | 3+ posts/week | Power User |
| Churned | No publish in 14 days | At-Risk |

**Weekly Retention Target (Month 3):** ≥ 40% of activated users post 3+/week.

---

### The "Wow" Threshold (Point of No Return)

**Wow Moment:** First **batch schedule of 3+ posts in a single session** — the user's content week is "queued and cleared" without leaving the Canva editor.

This is the moment the product becomes irreplaceable. A user who experiences this once will not willingly return to the manual workflow.

**Measurement:** Track "batch schedule sessions" (3+ scheduled posts in one session) as a distinct funnel event. Target: **25% of retained users** complete at least one batch schedule session per month.

---

## Business Objectives

**Deployment Model:** Canva Marketplace App — enables rapid iteration, zero infrastructure distribution overhead, and direct access to Canva's existing user base of 170M+ users.

**Revenue Model: Freemium**

| Tier | Features | Goal |
|---|---|---|
| **Free** | "Post Now" (instant publish), Basic caption editor, X feed preview | Acquisition & Activation |
| **Pro (Paid)** | Scheduling, Batch publish, Multi-account support, Analytics | Monetisation & Retention |

The free tier drives installs. The scheduling feature — the "Planning Job" — is the natural upgrade trigger. Users hit the free-tier wall exactly when they want to scale their workflow.

---

## Key Performance Indicators (KPIs)

### Growth KPIs

| KPI | Target | Timeframe |
|---|---|---|
| Monthly Active Users (MAU) | 5,000 MAU | 3 months post-launch |
| Install → First Publish conversion | ≥ 15% | Rolling 7-day window |
| Free → Pro upgrade rate | ≥ 8% of MAU | By month 3 |
| App Store Rating (Canva Marketplace) | ≥ 4.5 / 5.0 | Ongoing |

### Engagement KPIs

| KPI | Target | Timeframe |
|---|---|---|
| Power Users (3+ posts/week) | ≥ 40% of activated users | Month 3 |
| Batch Schedule Sessions | ≥ 25% of retained users/month | Month 3 |
| Time-to-Publish (North Star) | < 60 seconds (median) | From Day 1 |
| Publish Success Rate (no API errors) | ≥ 99% | Ongoing SLA |

### Technical / Reliability KPIs

| KPI | Target | Rationale |
|---|---|---|
| X API Error Rate | < 1% of publish attempts | Core trust signal |
| OAuth Re-auth Rate | < 5%/month | Stable connection = trust |
| Media Validation Pass Rate | ≥ 98% | Prevent silent failures |
| P95 Publish Latency | < 8 seconds | User perceives < 10s as instant |

---

## Anti-Metrics (Guardrails — What We Explicitly Avoid Optimising For)

> These are as important as the KPIs above. Optimising for the wrong signal will undermine the product's core value.

| Anti-Metric | Why We Avoid It | What We Track Instead |
|---|---|---|
| **Total Post Volume per User** | Incentivises spam; high volume ≠ high value | Publish *success rate* and *user satisfaction* |
| **Time Spent in Panel** | If the Speed Job is working, time-in-panel should be *low*. High dwell time means friction. | Time-to-Publish (should be **minimised**, not maximised) |
| **Daily Active Users (DAU)** | X is not a daily-open app for all personas; Priya opens Canva on Sundays. DAU will mislead. | Weekly Active Users (WAU) and Power User rate |
| **Feature Breadth Usage** | We don't want users exploring; we want them publishing. | Core journey completion rate (Compose → Validate → Publish) |

---
