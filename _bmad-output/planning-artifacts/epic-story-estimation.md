# Canva-X Integration: Epic & Story Estimations

This document provides a high-level agile estimation breakdown for the 5 established Epics and 19 Stories. Estimations are provided in abstract **Story Points** (where 1 point ≈ 0.5 Days of Developer Effort, factoring in testing and code review).

## Estimation Matrix

| Epic | Story | Description | Est. (Pts) | Open Questions / Assumptions |
| :--- | :--- | :--- | :--- | :--- |
| **1: Identity & Deferred Auth** | 1.1: Build Zero-State Deferred Auth UI | Construct the "unauthenticated" Settings UI allowing full draft interactions without a hard login wall. | 3 | **Assumption:** Canva Apps SDK permits interaction with native components (e.g., page selector) prior to OAuth configuration. |
| | 1.2: Canva OAuth Token Exchange | Implement `auth.requestAuthentication()` to securely ping X OAuth without leaving the editor iframe. | 5 | **Question:** Do strict browser pop-up blockers intercept the Canva native Auth modal? |
| | 1.3: Multi-Account Context Switching | Build the top profile card dropdown allowing power users to toggle between multiple authenticated X brands. | 3 | **Assumption:** The app natively limits a user to 5 stored X accounts simultaneously to prevent state bloat. |
| | 1.4: Strict Token Revocation | Bind a `DELETE` request to purge the specific account's OAuth tokens from Canva's secure storage cleanly. | 2 | **Question:** What happens if partial revocation occurs mid-publish? |
| **2: Core Post Composition** | 2.1: Canva Design Page Selection | Integrate the native component for mapping 1 to 4 Canva design slides into the payload state. | 2 | **Assumption:** SDK natively prevents a user from selecting >4 images if that constraint is passed. |
| | 2.2: Media Format & Cover Setup | Provide dropdowns for Image/Video/GIFs and support frame extraction for Video covers. | 5 | **Assumption:** Cover frames can be extracted via frontend React without requiring heavy FFMPEG re-encoding. |
| | 2.3: Live Captioning & Premium Override | Render the 280-char decrementing limit alongside the manual "Premium Override (25k)" checkbox fallback. | 3 | **Assumption:** Relying on user honesty for the 25k limit is accepted by stakeholders due to API blocks. |
| | 2.4: Frontend Constraints Validation | Hard-block the Publish execution button if X API limits (e.g., >5MB image, >140s video) are breached. | 2 | **Assumption:** X format parameters do not change dynamically; hardcoding these limits in frontend is stable. |
| **3: Advanced X-Native Tooling** | 3.1: Image Accessibility & Alt-Text Mapping | Dynamically render an Alt-Text input field strictly bound to each selected design page. | 3 | **Assumption:** Alt-Text payload is consistently maxed out at 1,000 characters per X documentation. |
| | 3.2: Multi-Page Thread Sequencing | For >4 slides, chain the SDK `publishContent` calls using the `in_reply_to_tweet_id` parameter to form threads. | 8 | **Question:** If Tweet 2 in a thread fails on X's end, is there any rollback mechanism? (Assume: No rollback possible). |
| | 3.3: Reply Controls UI Integration | Simple dropdown pushing the exact `reply_settings` enum string down the proxy. | 1 | **Assumption:** Just maps basic strings (Everyone, Mentioned, Following). |
| | 3.4: Degraded Tagging & Placebo Content Flags | UX fallbacks appending `@mentions` text to the main caption, and purely blurring the Canva preview on 'Sensitive'. | 3 | **Assumption:** Product Management officially signs off on rendering a placebo UI that doesn't ping the live Twitter API. |
| **4: WYSIWYG Real-Time Preview** | 4.1: Reactive Preview Scaffolding & DOM | A React layout actively watching the text state and re-rendering using exact native X typography/CSS. | 3 | **Assumption:** `@canva/app-ui-kit` does not block custom system font stacks like `-apple-system, BlinkMacSystemFont`. |
| | 4.2: Dynamic Asymmetrical Grid Rendering | The complex CSS Grid math replicating X's unique 1, 2, 3, and 4-image 50/50 vertical crop topologies. | 8 | **Question:** Are there precise formulas documented for X's vertical anchor crops, or is it purely manual visual emulation? |
| | 4.3: Overlay Badges & Sensitive CSS Blurs | Dynamic DOM injections for "GIF", "ALT", Play buttons, and the heavy frosted-glass sensitive blur. | 2 | **Assumption:** Heavy CSS backdrop blurs will not cause extreme performance drops in the Canva React iframe. |
| **5: Serverless Publish Execution** | 5.1: Zero-Retention Next.js Publish Proxy | The Next.js API route that accepts JWT Canvas tokens, executes the X `POST`, and instantly dies. | 5 | **Question:** Will Vercel serverless cold starts (often 1-3 seconds) violate the user's perception of "instant publish"? |
| | 5.2: MP4 Chunking Engine for Vercel Payload Limits | A frontend slice engine splitting heavy Video `Buffer` objects into <=3.9MB arrays to securely bypass 4.5MB Vercel hardcaps. | 8 | **Assumption:** Vercel limits are strictly constrained at the HTTP gateway level, making frontend chunking the only pure serverless solution. |
| | 5.3: Graceful Edge Error & 429 Trapping | Try/Catch blocks intercepting `429` statuses to parse `x-rate-limit-reset` Unix epochs into user-friendly UI Toast messages. | 3 | **Assumption:** The `x-rate-limit-reset` returned by X API is purely UTC epoch time. |
| | 5.4: Confirmation DOM & Live X Linking | Receiving `HTTP 201`, unmounting the Draft session data, and surfacing the live `https://x.com/...` URL. | 2 | **Assumption:** Once a session publishes, the "Draft" state is instantly purged; there is no history persistence required. |

## Capacity Summary
- **Total Stories:** 19
- **Total Estimated Effort:** 68 Story Points
- **Estimated Sprint Translation:** Assuming an average velocity of roughly ~20 points per 2-week Sprint for a standard integrated Dev Team, this Integration would require approximately **3 to 3.5 Sprints (~1.5 Months)**. The heaviest risk factors reside in the Serverless Video Chunking (Story 5.2) and the Asymmetrical Math for the Grid Simulator (Story 4.2).
