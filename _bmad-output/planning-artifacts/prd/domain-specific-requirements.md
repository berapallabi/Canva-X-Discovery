# Domain-Specific Requirements

## Platform Policy Constraints

- **Canva Marketplace Review:** App must pass Canva's submission review — Content Publisher intent correctly implemented, App UI Kit components only, accessible, internationalisation-ready, light/dark mode support, deferred auth pattern strictly followed.
- **X Developer Policy:** Explicit user-intent required for each publish action. Automated or bulk-API posting patterns prohibited. Tweet content may not be stored beyond the publishing workflow.
- **X API Access Tier:** Free API tier allows 500,000 tweets/month. Per-user OAuth distributes quota across accounts. Pro API tier required at > 500K monthly tweet volume.

## Technical Constraints

- **PublishRef Size Limit:** Canva SDK `PublishRef` payload capped at **5KB**. Caption + account ID + schedule timestamp must fit within this limit.
- **Media Specifications — X Feed Post:**
  - Images: JPG/PNG/WEBP, max 5MB, aspect ratio 16:9 or 1:1
  - Videos: MP4/MOV, max 512MB, max 2:20 duration, min 1280×720 resolution
- **OAuth Token Storage:** Access + refresh tokens must be stored server-side (encrypted at rest). Never in browser, frontend state, or Canva app storage.
- **Scheduling Infrastructure:** Requires a persistent backend service with job queue. Canva's Content Publisher intent provides no scheduling primitives.

## Risk Mitigations

| Risk | Mitigation |
|---|---|
| X API rate limits (429) | Per-user OAuth distributes quota; exponential backoff retry for schedule queue failures |
| Canva review rejection | Strictly follow Content Publisher intent design guidelines; no App UI Kit bypass |
| X platform policy violations | User-initiated publish only; no automated/bulk-API patterns |
| OAuth token expiry at scheduled delivery | Refresh token flow triggered on schedule execution; user notified if refresh fails |

---
