# Functional Requirements

## Account & Authentication Management

- **FR1:** Users can connect their X account to the app via OAuth 2.0 without leaving the Canva editor.
- **FR2:** Users can view which X account is currently active (handle + avatar displayed in Settings UI).
- **FR3:** Users can connect multiple X accounts and switch between them from the account selector.
- **FR4:** Users can disconnect their X account, which triggers immediate server-side token deletion.
- **FR5:** The app preserves the user's X authentication session across Canva editor sessions. The backend must securely map the stored X OAuth tokens to the user's verified Canva User ID.
- **FR6:** Users can interact with the Settings UI and preview their post before connecting their X account (deferred authentication). To build trust, the UI must explain the required X permissions (*offline.access, tweet.write*) immediately above the "Connect X" button prior to launching the OAuth pop-up.

## Content Configuration

- **FR7:** Users can write and edit a post caption within the Settings UI.
- **FR8:** Users can view a live character count for their caption (280-character standard; 25,000-character X Premium).
- **FR9:** Users can select which Canva design pages to include in their post (managed by Canva's page selector component). In the MVP, multiple selected pages are attached as multiple images to a single tweet (up to X's limit of 4 images per tweet).
- **FR10:** Users can select the output type for their post (X Feed Image Post or X Feed Video Post) from Canva's output type dropdown.
- **FR11:** The app validates media against X's technical specifications before publishing (format, file size, aspect ratio, video duration and resolution). The frontend must also validate that the total string payload (caption + metadata) fits within the strict 5KB Canva SDK `PublishRef` limit.
- **FR12:** Users receive clear, actionable error messages when media or payload validation fails, with specific guidance on how to resolve the issue.

## Preview

- **FR13:** Users can view a real-time WYSIWYG preview of their post as it will appear in the X feed, explicitly simulating X's specific center-cropping logic for irregular aspect ratios.
- **FR14:** The preview updates dynamically as the user edits their caption or modifies the Canva design, correctly scaling and cropping.
- **FR15:** The preview renders in both X light mode and dark mode.
- **FR16:** The preview displays the active X account handle and avatar.
- **FR17:** The preview shows appropriate loading states — placeholder image for images; progress bar for videos.

## Instant Publishing

- **FR18:** Users can publish their Canva design directly to X as a tweet without downloading any files.
- **FR19:** Users receive a publish confirmation with a direct URL link to the live tweet on X.
- **FR20:** The app returns the live tweet URL, which Canva displays as the primary action in the post-publish success dialog.
- **FR21:** Users can initiate X account authentication from within the publish flow if not previously connected.

## Error Handling & Reliability

- **FR30:** The app surfaces X API errors to the user with clear, platform-specific error messages (e.g., session expired, duplicate content, media rejected).
- **FR31:** The stateless proxy implements transient retry logic for immediate X API failures (429 rate limit, 503 server errors).

---
