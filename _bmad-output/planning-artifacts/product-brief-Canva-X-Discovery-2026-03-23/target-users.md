# Target Users

## Primary Users

---

### Persona 1: "Alex" — The Social Media Manager (SMM)

**Role:** Social Media Manager — operates both in-agency (managing 5–10 client brands) and in-house (single brand, multiple channels: X, LinkedIn, Instagram)

**Context:** Alex spends 60% of their day inside Canva creating visual assets. The other 40% is split across scheduling tools (Buffer, Later), content calendars (Notion), and approval workflows (Slack). Each platform is a context switch, and each context switch costs time and creates potential for version confusion.

**Motivations:** Efficiency, consistency, professional output at scale. Alex's reputation depends on posting the right asset to the right account at the right time.

**Current Workaround:** Downloads assets into organized (but ever-growing) local folders. Renames files with client/date/version codes. Manually uploads to each platform. Tracks what's been posted in a separate spreadsheet.

**Pain Points:**
- Managing downloads across multiple client folders creates "organizational debt" that compounds weekly.
- A single brand misalignment caught at upload time triggers a painful re-edit cycle.
- Canva's native scheduler is unreliable for video and breaks OAuth connections without warning.
- Cannot manage multiple X accounts from one unified Canva panel.

**Success Vision:** *"I finish designing a post in Canva, click 'Publish to X,' select the client account, write the caption, and it's live. No downloads. No folder management. Done."*

**"Aha!" Moment:** First time they publish to a client X account directly from Canva in under 60 seconds — without touching their Downloads folder.

---

### Persona 2: "Priya" — The Solo Growth Creator

**Role:** Solo Content Entrepreneur / Independent Thought Leader on X

**Goal:** Audience growth and thought leadership in their niche (e.g., design, tech, finance, wellness).

**Context:** Priya batch-creates content on weekends — a full Sunday session producing 10–15 visual assets in Canva. She then posts daily throughout the week to maintain relevance and engagement. She has no production team; Canva *is* her production team.

**Motivations:** Building a high-quality aesthetic at solo-creator speed. She wants her X profile to look like it has a team behind it, even though it's just her.

**Current Workaround:** Exports all designs as a batch download to a phone or desktop folder. Manually posts each day using X's native uploader, adding captions on the fly.

**Pain Points:**
- The "batch download → daily manual post" loop is tedious and error-prone.
- She sometimes forgets which version of an asset was the final one (the `_v2_FINAL_USE_THIS.png` problem).
- X's native uploader offers no design preview — what she sees in Canva is never quite what appears in the feed.

**Success Vision:** *"I batch-schedule all my week's visual content on Sunday from within Canva, set the times, and forget about it. My audience sees polished, on-brand posts every day — and I didn't have to touch X once."*

**"Aha!" Moment:** First Sunday batch-schedule session where she plans the entire week's X content without leaving Canva — and the posts go live exactly as they appeared in the preview.

---

## Secondary Users

### Persona 3: "Marco" — The Real-Time Marketer

**Role:** Small business owner or community manager who reacts to trends (newsjacking).

**Context:** Marco doesn't pre-plan content. He responds to what's happening — a viral trend, a product launch, a breaking news story relevant to his brand. Speed is his primary lens. He's the person who creates a quick branded graphic in Canva in 10 minutes and needs it live on X *right now* before the trend peaks.

**Needs:**
- A **"Post Now"** button that works instantly, with zero setup friction.
- No scheduling, no calendar, no analytics — just *post and done*.
- The simpler the interface, the better.

**Pain Point:** By the time he downloads and uploads, the conversation has moved on. The 2-minute delay at the end of a 10-minute creation sprint is a disproportionate frustration.

**Role in Product:** Secondary. The **Speed Job** must be flawless — Marco is the canary in the coal mine for publish reliability. If Quick Publish works for Marco in a trend moment, it works for everyone.

---

## Jobs-to-Be-Done (Ranked by Priority)

| Priority | Job Statement | Primary Persona | MVP Scope |
|---|---|---|---|
| 🥇 **Primary** | *"Get this design live on X in under 60 seconds."* | All segments | ✅ MVP North Star |
| 🥈 **Secondary** | *"Schedule my week's visual content without leaving my canvas."* | Priya (Solo Creator), Alex (SMM) | ✅ MVP |
| 🥉 **Tertiary** | *"Ensure my crop and text look right in the X feed preview."* | Priya, Alex | ✅ MVP |

> **North Star Metric (JTBD-derived):** Time from "design complete" to "post live on X" — target: **< 60 seconds**.

---

## User Journey Map

### Alex (SMM) — Typical Publishing Journey

```
[In Canva Editor]
  ↓ Design complete
  ↓ Opens "Publish to X" side panel
  ↓ Selects X account (from connected accounts list)
  ↓ Sees WYSIWYG feed preview (light/dark mode)
  ↓ Writes caption in Caption Toolkit (character counter active)
  ↓ Chooses: "Post Now" OR "Schedule"
  ↓ Clicks "Post Now"
  ↓ Media validated → uploaded via X API v2 → tweet created
  ✅ "Posted!" confirmation with X link — still in Canva
```

### Priya (Solo Creator) — Batch Scheduling Journey

```
[Sunday Canva Session]
  ↓ Designs 10 posts across multiple Canva pages
  ↓ Opens "Publish to X" panel
  ↓ Selects page → previews in X feed format
  ↓ Writes caption + schedules for Monday 9am
  ↓ Selects next page → schedules for Tuesday 11am
  ↓ ... repeats for all 10 posts
  ✅ Week's content queued — never left Canva
```

### Marco (Real-Time Marketer) — Trend Response Journey

```
[Breaking trend spotted]
  ↓ Opens Canva, creates quick branded graphic (10 mins)
  ↓ One tap: "Post Now" from side panel
  ↓ Adds 2-line caption
  ↓ Clicks "Post Now"
  ✅ Live on X in under 60 seconds — trend still active
```

---
