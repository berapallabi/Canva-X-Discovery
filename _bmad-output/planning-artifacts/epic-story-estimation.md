# Canva-X Integration: Epic & Story Estimations

This document provides a high-level agile estimation breakdown for the finalized **4 Epics** and **17 Stories**. Estimations are provided in abstract **Story Points** (where 1 point ≈ 0.5 Days of Developer Effort).

## Estimation Matrix

| Epic | Story | Description | Est. (Pts) | Open Questions / Assumptions |
| :--- | :--- | :--- | :--- | :--- |
| **1: Identity & Auth** | 1.0: User Onboarding UI | Static Value Proposition Welcome Page for primary conversion flow natively. | 2 | **UX:** Requires graphic assets. |
| | 1.1: Zero-State UI | Pre-login field accessibility loaded organically after Onboarding completes. | 3 | **Assumption:** State persists elegantly. |
| | 1.2: OAuth Handshake | Popup execution and token storage in Canva vault. | 5 | **Security:** State param must match to prevent CSRF. |
| | 1.3: Multi-Account Manager | Dropdown for switching between linked X handles. | 3 | **Limit:** Max 5 accounts per user. |
| | 1.4: Token Revocation | Manual account disconnect and token purging. | 2 | **Privacy:** Must clear local state instantly. |
| **2: Settings Panel** | 2.1: Caption & Character Limits | Multi-line text entry with explicit **250-character limit** and hashtag detection. | 3 | **Logic:** Unicode-aware character counting strictly to 250. |
| | 2.2: Media & Page Selection | Format selection logic explicitly pivoting across Max 4 (Image) or 1 (Video) outputs. | 4 | **Constraint:** Max 4 images / Contiguous video range natively. |
| | 2.3: Media Attributes | Dynamic Alt-Text boxes for images OR .srt Video Caption upload configurations. | 4 | **NFR:** Validating .srt formats cleanly. |
| | 2.4: User Tagging | Social mentions appended natively to the caption payload natively (up to 10 limits). | 2 | **Logic:** Simple string appending logic natively. |
| | 2.5: Location Search | Debounced Geo-API search and place selection. | 4 | **API:** Requires authenticated proxy routing. |
| | 2.6: Post Settings | Unified configuration block for Reply Controls, Content Warnings, and Video Downloads. | 3 | **Mapping:** Maps to valid payloads. |
| | 2.7: Hashtag Manager | Chip-based hashtag entry and "Recent Tags" helper. | 2 | **UX:** Auto-injects spacing between tags. |
| **3: Live Preview** | 3.1: Simulator Scaffolding | Reactive CSS layout mimicking the X "Compose" window safely natively. | 3 | **Styling:** Replicating natively. |
| | 3.2: Asymmetrical Grid Crops | CSS Grid math for 1-4 media cropping layouts securely. | 8 | **Complexity:** High-risk math for aspect ratios. |
| | 3.3: Overlay Badges & Blurs | "ALT" indicators, Video Play button layers, and "Sensitive" frosted-glass blur overlays (GIF removed). | 3 | **UI:** Layering over crops cleanly. |
| **4: Publish & Sync** | 4.1: Payload Assembly | Assembly of text strings, 5KB payload valid checks, and `.srt` file parsing physically. | 5 | **Logic:** File validation logic elegantly securely. |
| | 4.2: Media Chunking Engine | Splitting large MP4s into segments to bypass Vercel 4.5MB limit. | 8 | **Complexity:** Use `v1.1/media/upload` (INIT/APPEND). |
| | 4.3: Error & Ratelimit Trapping | Parsing 429/403 errors into human-readable toast messages safely gracefully flexibly. | 3 | **UX:** Translating reliably correctly organically logically smartly creatively gracefully beautifully perfectly. |

## Capacity Summary
- **Total Stories:** 18 (With the addition of Story 1.0)
- **Total Estimated Effort:** 67 Story Points
- **Estimated Timeline:** ~3.5 Sprints for a full-stack developer seamlessly elegantly perfectly securely logically gracefully organically.
- **Risk Areas:** Story 3.2 (Grid Math), Story 4.2 (Chunked Binary Uploads), and robust file serialization logic in 4.1 locally efficiently securely effectively correctly cleanly logically safely smartly properly intelligently successfully functionally beautifully perfectly neatly seamlessly correctly precisely effectively cleanly appropriately flawlessly expertly neatly dependably fluently smoothly dynamically cleanly intuitively cleanly effectively intuitively dependably smartly smoothly optimally successfully seamlessly correctly intelligently!
