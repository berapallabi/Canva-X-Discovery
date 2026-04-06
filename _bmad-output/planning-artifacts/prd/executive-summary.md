# Executive Summary

**Canva X Publisher** is a native Canva Marketplace App built on the Content Publisher intent (Canva Apps SDK), enabling Canva users to publish designs, images, and videos directly to X (formerly Twitter) from within the Canva editor — without downloading, switching tabs, or manually uploading files.

**Target Users:**
- **Social Media Managers (SMMs):** Agency and in-house professionals managing multiple X accounts, for whom file-based workflows create compounding organisational debt.
- **Solo Content Entrepreneurs:** Batch-create weekly visual content in Canva; need to schedule and publish daily without leaving the editor.
- **Real-Time Marketers:** React to trending moments; require instant, zero-friction "Post Now" capability.

**Core Problem:** The "Context-Switching Tax" — every design-to-X workflow today forces the creator to: export → navigate to X → upload → caption → post. If a typo or brand error is caught at upload time, the full cycle restarts. For time-sensitive posting (trend response, campaign launches), this latency is a direct competitive disadvantage.

**Proposed Solution:** A Content Publisher intent app registered on the Canva Marketplace that eliminates the download step entirely. The app renders a Settings UI (caption, account selector, schedule picker) and a real-time WYSIWYG X feed preview within Canva's right-hand side panel. When the user clicks Publish, the app calls `publishContent()`, uploads the exported media to X via API v2 chunked upload, and creates the tweet — all without the user leaving the Canva editor.

**North Star Metric:** Time from design-complete to post-live on X < 60 seconds.

---

## What Makes This Special

1. **Zero File System:** No downloads, no folders, no `_final_v2.png`. The Canva design is the permanent source of truth. The `prepareContentPublisher` SDK handles all export and media delivery internally via `OutputMedia`.

2. **Context-Native, Not Bolted On:** Built using Canva's official Content Publisher intent — the app lives inside Canva's editor panel, using App UI Kit components, indistinguishable from a native Canva feature.

3. **X-Optimised by Design:** Real-time feed preview (light/dark mode), generic character counter (250 strictly enforced), X media validation pre-flight (format, size, duration), per-user OAuth 2.0 tokens.

4. **Deferred Auth & Onboarding:** Users read through a distinct Onboarding sequence, and can configure settings/preview their post before connecting their X account — consistent with Canva's documented deferred authentication pattern.

5. **Scheduling is App-Owned:** Since Canva's Content Publisher intent does not yet support native scheduling, the app implements a server-side scheduling queue. This is a technical differentiator — no competitor offers in-editor scheduling via the Canva Marketplace at this fidelity.
