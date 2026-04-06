# Functional Requirements

## 1. Account & Authentication Management

### 1.1 Pre-Login (Unauthenticated State)
- **FR1.1:** Upon launching the app, if no valid X OAuth token exists for the user's Canva ID, the app MUST display the "Unauthenticated State" UI.
- **FR1.1b:** If the user is a first-time user, the app MUST display the User Onboarding Page explaining the integration value before navigating to the core UI settings.
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

## 2. Content Configuration & Settings

- **FR7:** Users can write and edit a post caption within the Settings UI to describe their post.
- **FR8:** Users can view a live character count for their caption, enforcing a strict maximum of **250 characters** for all posts.
- **FR9:** Users can select which Canva design pages to include in their post (managed by Canva's page selector component). A maximum of 4 pages per post is enforced.
- **FR10:** The output is a **Fixed format type - Post**, eliminating any dropdown menu for "Image" vs "Video" types natively. The format dynamically adapts based on the uploaded media properties.
- **FR10.1:** Users can explicitly flag their attached media with Content Warnings by marking a specific category (e.g., Sensitive, Violence) to ensure compliance with X's safety and publishing policies. **[BLOCKED BY X API v2]** The UI MUST provide a blurred CSS mockup for the Canva Preview only, relying on the user's explicit X account defaults for the live post.
- **FR10.2:** If an **Image slot** is selected, a dedicated Alt-Text text field gets enabled for accessibility compliance.
- **FR10.3:** If a **Video slot** is selected, a dedicated "Video Caption" upload field gets enabled, allowing the user to attach an explicit subtitle/transcription file (e.g., `.srt`) distinct from the post body text.
- **FR10.4:** Users can tag existing X accounts in the post using an explicit "Tag People" field logic. This operates at the post level (up to 10 accounts) and degrades gracefully to appending `@mentions` at the end of the text Payload natively.
- **FR10.5:** The app provides a localized "Trending Hashtags" typeahead suggestion tool within the caption editor to boost post engagement.
- **FR10.6:** Users can attach geographical Location tags to their published posts.
- **FR10.7:** Users can configure Reply Controls prior to publishing, restricting who can engage with their post on X. The 4 supported options are: Everyone, Accounts you follow, Only accounts you mention, and Verified accounts.
- **FR10.8:** Users can act on a specific "Enable download video" options toggle directly in the Post Settings panel to allow or restrict users down-stream from downloading the video.
- **FR11:** The app validates media against X's technical specifications before publishing (format, file size, aspect ratio, video duration and resolution). The frontend must also validate that the total string payload fits within the strict 5KB Canva SDK `PublishRef` limit.
- **FR12:** Users receive clear, actionable error messages when media or payload validation fails, with specific guidance on how to resolve the issue.

## 3. Preview (High-Fidelity WYSIWYG Simulator)

The Preview UI is the primary user value addition, granting creators absolute confidence by ensuring "what you see is what you publish." It strictly mimics the native X user interface logic.

### 3.1 Preview Anatomy
- **FR13:** The preview contains four distinct structural zones identical to X feeds:
  - **Header:** Dynamic Author Name, Verified Badge (if applicable based on user data), `@handle`, and a mocked time string (e.g., "Just now").
  - **Body (Caption):** Rendered text accurately reflecting X's typography (system fonts), line heights, truncation logic, and dynamic highlighting for `@mentions`, hashtags, and URLs.
  - **Media Core:** The visual payload (Canvas/Video) framed precisely to match X's feed rules.
  - **Footer (Engagement):** Mocked system icons for Reply, Repost, Like, Bookmark, and Share.

### 3.2 Media Grid & Formatting Logic
- **FR14:** **Single Image:** Renders at full width. Explicitly simulates X's vertical cropping logic for extreme aspect ratios.
- **FR15:** **Multi-Image Composites:** Accurately renders X's native multi-image grid templates based on Canva page selection:
  - *2 Items:* Side-by-side 50/50 vertical split crop.
  - *3 Items:* One large anchor image (left), two smaller vertically stacked images (right).
  - *4 Items:* 2x2 equal grid crop matrix.
- **FR16:** **Video Formatting:** Simulates the native X video player framing. Displays a centered, mocked "Play" button overlay.

### 3.3 Dynamic Overlays & Accessibility
- **FR18:** **Alt-Text Badge:** If the user adds Alt-Text (FR10.2), the preview dynamically renders the native solid black "ALT" badge in the bottom-left corner of the associated image.
- **FR19:** **Sensitive Content Warning:** If Content Warnings are activated (FR10.1), the preview replaces the image with X's frosted glass "The following media includes potentially sensitive content" warning overlay simulation.

### 3.4 Live Responsiveness
- **FR20:** DOM updates are instantaneous. The preview re-renders in real-time as the user types their caption or swaps Canva design pages.
- **FR21:** Users can toggle the preview container between **X Light Mode**, **X Dark Mode**, and **X Dim Mode** environments to ensure sufficient contrast across all common user devices.

## 4. Instant Publishing

- **FR22:** Users can publish their Canva design directly to X as a post without downloading any files.
- **FR23:** Users receive a publish confirmation with a direct URL link to the live post on X.
- **FR24:** The app returns the live post URL, which Canva displays as the primary action in the post-publish success dialog.
- **FR25:** Users can initiate X account authentication from within the publish flow if not previously connected.

## 5. Error Handling & Reliability

- **FR30:** The app surfaces X API errors to the user with clear, platform-specific error messages (e.g., session expired, duplicate content, media rejected).
- **FR31:** The stateless proxy implements transient retry logic for immediate X API failures (429 rate limit, 503 server errors).
