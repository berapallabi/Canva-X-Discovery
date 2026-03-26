# Functional Requirements

## 1. Account & Authentication Management

### 1.1 Pre-Login (Unauthenticated State)
- **FR1.1:** Upon launching the app, if no valid X OAuth token exists for the user's Canva ID, the app MUST display the "Unauthenticated State" UI.
- **FR1.2:** The unauthenticated UI MUST allow users to interact with the Settings UI (drafting captions, selecting media) and view the Preview panel before logging in (Deferred Authentication).
- **FR1.3:** A primary "Connect to X" CTA MUST be persistent in the Settings panel. Directly above this CTA, the UI MUST explicitly list the exact scopes requested during OAuth: `tweet.read`, `tweet.write`, `users.read`, `offline.access`.
- **FR1.4:** Clicking "Connect to X" MUST trigger Canva's native `auth.requestAuthentication()` SDK method, launching a secure OAuth 2.0 popup without navigating the user away from the active Canva Editor iframe.

### 1.2 Post-Login (Authenticated State)
- **FR2.1:** Upon successful OAuth token exchange, the UI MUST seamlessly transition to the "Authenticated State" without requiring a panel reload.
- **FR2.2:** The Settings UI MUST display an Account Profile Card representing the active session. This card MUST surface exact data extracted from the X `users/me` endpoint: User Display Name, `@handle`, and Profile Avatar Image URL.
- **FR2.3:** The authentication session MUST be persistent. The background Next.js proxy MUST safely leverage Canva's native `auth` secure storage token APIs to retrieve the X Bearer and Refresh tokens upon subsequent editor loads.

### 1.3 Multi-Account Support & Disconnection
- **FR3.1:** Users MUST be able to connect multiple distinct X accounts securely under a single Canva User ID context.
- **FR3.2:** The Account Profile Card MUST operate as a dropdown Account Selector. When clicked, it MUST render a list of all currently authenticated X accounts.
- **FR3.3:** Selecting an alternate account from the dropdown MUST seamlessly switch the active publishing context and immediately update the `@handle` and Avatar reflected in the WYSIWYG Preview panel.
- **FR3.4:** Every connected account in the dropdown MUST feature a distinct "Disconnect" action.
- **FR3.5:** Clicking "Disconnect" MUST trigger a protective confirmation dialog. Upon user approval, the app MUST explicitly execute a DELETE request to purge the associated X OAuth tokens from Canva's secure storage API, completely revoking local access and returning the user to the Unauthenticated State if no other accounts remain.

## Content Configuration

- **FR7:** Users can write and edit a post caption within the Settings UI.
- **FR8:** Users can view a live character count for their caption (280-character standard; 25,000-character X Premium).
- **FR9:** Users can select which Canva design pages to include in their post (managed by Canva's page selector component). In the MVP, multiple selected pages are attached as multiple images to a single tweet (up to X's limit of 4 images per tweet).
- **FR10:** Users can select the output type for their post (X Feed Image Post or X Feed Video Post) from Canva's output type dropdown.
- **FR10.1:** Users can explicitly select the specific media format (Static Image, Video, or Animated GIF), enabling format-specific X API validations.
- **FR10.2:** For video and GIF formats, users can select a specific frame from the assigned media or upload a custom file to serve as the Cover Image.
- **FR10.3:** Users can tag existing X accounts (@mentions) in visual media via a dedicated "Tag People" interface, just like the native X app.
- **FR10.4:** The app provides a localized "Trending Hashtags" typeahead suggestion tool within the caption editor to boost post engagement.
- **FR10.5:** Users can add explicit Alt-Text descriptions to all attached media directly from the Settings UI for accessibility compliance.
- **FR10.6:** Users can author Threaded Posts (multi-tweet threads) to bypass standard character limits, chaining multiple Canva pages into sequential tweets.
- **FR10.7:** Users can attach geographical Location tags to their published posts.
- **FR10.8:** Users can configure Reply Controls prior to publishing, restricting who can engage with their post on X (e.g., Everyone, Accounts you follow, Verified accounts, or Only accounts you mention).
- **FR10.9:** Users can explicitly flag their attached media with Content Warnings (Sensitive Media tag) to ensure compliance with X's safety and publishing policies.
- **FR11:** The app validates media against X's technical specifications before publishing (format, file size, aspect ratio, video duration and resolution). The frontend must also validate that the total string payload (caption + metadata) fits within the strict 5KB Canva SDK `PublishRef` limit.
- **FR12:** Users receive clear, actionable error messages when media or payload validation fails, with specific guidance on how to resolve the issue.

## Preview (High-Fidelity WYSIWYG Simulator)

The Preview UI is the primary user value addition, granting creators absolute confidence by ensuring "what you see is what you publish." It strictly mimics the native X user interface logic.

### Preview Anatomy
- **FR13:** The preview contains four distinct structural zones identical to X feeds:
  - **Header:** Dynamic Author Name, Verified Badge (if applicable based on user data), `@handle`, and a mocked time string (e.g., "Just now").
  - **Body (Caption):** Rendered text accurately reflecting X's typography (system fonts), line heights, truncation logic ("Show more" above 280 characters), and dynamic highlighting for `@mentions`, hashtags, and URLs (rendered in X's signature blue).
  - **Media Core:** The visual payload (Canvas/Video/GIFs) framed precisely to match X's feed rules.
  - **Footer (Engagement):** Mocked system icons for Reply, Repost, Like, Bookmark, and Share to provide scale and geographical context.

### Media Grid & Formatting Logic
- **FR14:** **Single Image:** Renders at full width. Explicitly simulates X's vertical cropping logic for extreme aspect ratios (e.g., tall infographics) so users can detect safe-zone bleed.
- **FR15:** **Multi-Image Composites:** Accurately renders X's native multi-image grid templates based on Canva page selection:
  - *2 Images:* Side-by-side 50/50 vertical split crop.
  - *3 Images:* One large anchor image (left), two smaller vertically stacked images (right).
  - *4 Images:* 2x2 equal grid crop matrix.
- **FR16:** **Video Formatting:** Simulates the native X video player framing. Displays a centered, mocked "Play" button overlay and a duration badge (e.g., "0:15") in the bottom-right corner. It defaults to the selected Video Cover Image (from FR10.2).
- **FR17:** **GIF Formatting:** Simulates X's looping behavior by displaying the native "GIF" badge overlay in the bottom-left corner of the media block.

### Dynamic Overlays & Accessibility
- **FR18:** **Alt-Text Badge:** If the user adds Alt-Text (FR10.5), the preview dynamically renders the native solid black "ALT" badge in the bottom-left corner of the associated image.
- **FR19:** **Sensitive Content Warning:** If Content Warnings are activated (FR10.9), the preview replaces the image with X's frosted glass "The following media includes potentially sensitive content" warning overlay simulation.

### Live Responsiveness
- **FR20:** DOM updates are instantaneous. The preview re-renders in real-time as the user types their caption or swaps Canva design pages.
- **FR21:** Users can toggle the preview container between **X Light Mode**, **X Dark Mode**, and **X Dim Mode** environments to ensure sufficient contrast across all common user devices.

## Instant Publishing

- **FR18:** Users can publish their Canva design directly to X as a tweet without downloading any files.
- **FR19:** Users receive a publish confirmation with a direct URL link to the live tweet on X.
- **FR20:** The app returns the live tweet URL, which Canva displays as the primary action in the post-publish success dialog.
- **FR21:** Users can initiate X account authentication from within the publish flow if not previously connected.

## Error Handling & Reliability

- **FR30:** The app surfaces X API errors to the user with clear, platform-specific error messages (e.g., session expired, duplicate content, media rejected).
- **FR31:** The stateless proxy implements transient retry logic for immediate X API failures (429 rate limit, 503 server errors).

---
