# Section 3: Technology Landscape

## 3.1 Canva Apps SDK — Publish Extensions

Canva's developer platform enables first-party and third-party integrations through:

| Integration Type | Description |
|---|---|
| **Canva Apps SDK** | JavaScript apps running in an iframe within the Canva editor |
| **Publish Extensions** | Add a publish destination to Canva's "Publish" menu — directly relevant |
| **Connect APIs** | REST APIs to integrate Canva into external tools |
| **POST /publish/resources/upload** | API endpoint used by publish extensions to push designs to destination platforms |

**Publish Extensions** are the most relevant technology. They allow:
- A "Publish to X" button natively within Canva's Share → Publish menu
- Support for formats: JPG, PNG, PDF, PPTX, and potentially MP4 (video)
- Authentication bridging via Canva's User API (OAuth)
- Custom backend logic via the Fetch API (sending HTTP requests to X API)

> **Technical Feasibility: High** — Canva's SDK explicitly supports third-party publish extensions. This is the correct technical pathway for building a native "Publish to X" integration.

## 3.2 X (Twitter) API v2 — Media Publishing

**Key API Capabilities (2024–2025):**

| Feature | Details |
|---|---|
| Media Upload Endpoint | `POST /2/media/upload` (deprecated v1.1 as of March 31, 2025) |
| Auth Required | OAuth 2.0 with `media.write` scope + User token |
| Image Support | JPEG, PNG, GIF — max 5MB per image |
| Video Support | MP4/MOV — up to 512MB, 2m20s (standard); up to 16GB, 4hr (X Premium) |
| Chunked Upload | Required for large video files (multi-part upload) |
| Post Endpoint | `POST /2/tweets` with `media.media_ids` field |

**Publishing Flow:**
```
Step 1: Upload media → POST /2/media/upload → receive media_id
Step 2: Create tweet → POST /2/tweets { text: "...", media: { media_ids: [media_id] } }
```

> **Confidence: High** — Confirmed via official X Developer documentation and Stack Overflow community validations.

## 3.3 X API Pricing (Critical Risk)

X's API pricing has become a significant barrier for developers (2024–2026):

| Tier | Monthly Cost | Posts/month (app-level) | Reads/month |
|---|---|---|---|
| Free | $0 | 500 | 100 |
| Basic | $200/month | 50,000 | 15,000 |
| Pro | $5,000/month | 300,000 | 1,000,000 |
| Enterprise | Custom (~$40K+/month) | Unlimited | Full stream |

> ⚠️ **Critical Risk:** The X API's high pricing is a significant constraint. For a Canva integration serving millions of users, the Basic tier ($200/mo) allows only 50,000 posts/month at the app level — insufficient for scale. Enterprise pricing would be required, adding substantial platform cost.

**Mitigation strategies:**
- Per-user OAuth tokens (each user uses their own API limits, not the app's)
- Canva negotiating a platform-level partnership with X (precedent: Buffer, Hootsuite have enterprise deals)
- X API's Free tier supports 500 write posts/user/month — sufficient for casual users with per-user auth

---
