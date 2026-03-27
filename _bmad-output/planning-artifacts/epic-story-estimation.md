# Canva-X Integration: Epic & Story Estimations

This document provides a high-level agile estimation breakdown for the finalized **4 Epics** and **17 Stories**. Estimations are provided in abstract **Story Points** (where 1 point ≈ 0.5 Days of Developer Effort).

## Estimation Matrix

| Epic | Story | Description | Est. (Pts) | Open Questions / Assumptions |
| :--- | :--- | :--- | :--- | :--- |
| **1: Identity & Auth** | 1.1: Zero-State Onboarding UI | Connect button and pre-login field accessibility (Deferred Auth). | 3 | **Assumption:** State persists after popup closes. |
| | 1.2: OAuth Handshake | Popup execution and token storage in Canva vault. | 5 | **Security:** State param must match to prevent CSRF. |
| | 1.3: Multi-Account Manager | Dropdown for switching between linked X handles. | 3 | **Limit:** Max 5 accounts per user. |
| | 1.4: Token Revocation | Manual account disconnect and token purging. | 2 | **Privacy:** Must clear local state instantly. |
| **2: Settings Panel** | 2.1: Caption & Character Limits | Multi-line text entry with 280/25k logic and hashtag detection. | 3 | **Logic:** Unicode-aware character counting. |
| | 2.2: Media & Page Selection | Format selection (Video/Image) pivoting the grid constraints. | 4 | **Constraint:** Max 4 images / Contiguous video range. |
| | 2.3: Media Accessibility | Dynamic Alt-Text boxes for each selected page. | 3 | **NFR:** Max 1000 characters per box. |
| | 2.4: User Tagging | Social mentions appended to caption (with API v2 warnings). | 2 | **Logic:** Simple string appending logic. |
| | 2.5: Location Search | Debounced Geo-API search and place selection. | 4 | **API:** Requires authenticated proxy routing. |
| | 2.6: Reply Controls | Dropdown for Everyone/Following/Mentioned privacy. | 1 | **Mapping:** Maps to X API v2 enum. |
| | 2.7: Hashtag Manager | Chip-based hashtag entry and "Recent Tags" helper. | 2 | **UX:** Auto-injects spacing between tags. |
| **3: Live Preview** | 3.1: Simulator Scaffolding | Reactive CSS layout mimicking the X "Compose" window. | 3 | **Styling:** Replicating X's proprietary typography. |
| | 3.2: Asymmetrical Grid Crops | CSS Grid math for 1/2/3/4 image cropping topologies. | 8 | **Complexity:** High-risk math for aspect ratios. |
| | 3.3: Overlay Badges & Blurs | "GIF/ALT" pills and "Sensitive" frosted-glass blur. | 3 | **UI:** Layering over the grid crops. |
| **4: Publish & Sync** | 4.1: Payload Assembly | Final validation of all fields before transmitting to proxy. | 5 | **Logic:** Cryptographic Canva JWT signing. |
| | 4.2: Media Chunking Engine | Splitting large MP4s into segments to bypass Vercel 4.5MB limit. | 8 | **Complexity:** Use `v1.1/media/upload` (INIT/APPEND). |
| | 4.3: Error & Ratelimit Trapping | Parsing 429/403 errors into human-readable toast messages. | 3 | **UX:** Translating raw JSON errors to clear advice. |

## Capacity Summary
- **Total Stories:** 17
- **Total Estimated Effort:** 62 Story Points
- **Estimated Timeline:** ~3 Sprints (6 Weeks) for a full-stack developer.
- **Risk Areas:** Story 3.2 (Grid Math) and Story 4.2 (Chunked Binary Uploads).
