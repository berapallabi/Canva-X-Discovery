# User Journeys

## Journey 1: Alex — Social Media Manager ("Speed Mode")

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

## Journey 2: Priya — Solo Creator ("Planning Mode")

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

## Journey 3: Marco — Real-Time Marketer ("Trend Response Mode")

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

## Journey 4: Admin / Ops — Scheduled Post Failure Recovery

**Persona:** The backend operator monitoring the scheduling queue for errors and X API rate-limit events.

**Opening Scene:** Scheduling service fires at 9:00am for Priya's Monday post. X API returns 429 (rate limit exceeded).

**Journey:**
1. System marks the post `retry_pending`.
2. Exponential backoff retries: +5min, +15min, +30min.
3. After 3 failed retries, post is marked `failed`.
4. Next time Priya opens the app: in-panel notification — "Your Monday 9am post failed to publish. Retry?"

**Capabilities Revealed:** Scheduling queue with retry/backoff, failed delivery notifications, manual retry capability, ops logging.

---

## Journey Requirements Summary

| Journey | Capabilities Required |
|---|---|
| Alex (Speed) | Multi-account, Post Now, real-time preview, post-publish URL |
| Priya (Planning) | Scheduling, timezone picker, queue management, preview sync |
| Marco (Trend) | Token persistence, fast-path publish |
| Admin (Ops) | Retry logic, failure notifications, queue observability |

---
