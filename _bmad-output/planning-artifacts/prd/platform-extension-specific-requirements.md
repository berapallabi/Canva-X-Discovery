# Platform Extension Specific Requirements

## Technical Architecture

```
[Canva Editor — Content Publisher Side Panel (SDK iframe)]
    ├── Settings UI (renderSettingsUi)
    │     ├── X Account Selector (handle + avatar)
    │     ├── Caption Editor (character counter)
    │     ├── Schedule Picker (date/time/timezone)
    │     └── OAuth Connect button (deferred auth)
    ├── Preview UI (renderPreviewUi)
    │     └── WYSIWYG X Feed Simulation (light/dark)
    └── publishContent()
            ├── Receive OutputMedia (Canva-exported files)
            ├── Upload to X via POST /2/media/upload (chunked)
            └── Create tweet via POST /2/tweets

[Backend Service]
    ├── OAuth Token Manager (store, refresh, revoke)
    ├── Scheduling Queue (job scheduler + retry logic)
    └── X API Proxy (media upload, tweet creation)
```

## SDK Integration Reference

| SDK Component | Usage |
|---|---|
| `prepareContentPublisher()` | Entry point — registers all 4 intent functions |
| `getPublishConfiguration` | Defines OutputType (X Feed Image/Video specs) |
| `renderSettingsUi` | Caption, account selector, schedule picker |
| `renderPreviewUi` | Live X feed preview iframe |
| `publishContent` | Receives OutputMedia, calls X API, returns post URL |
| `auth.initOauth()` | X OAuth 2.0 flow setup |
| `oauth.getAccessToken()` | Token retrieval on settings load |
| `oauth.requestAuthorization()` | OAuth pop-up trigger |
| `validityState` | Controls Publish button visibility |

## Output Type Configuration

```
X Feed Post (Image):
  - Format: JPG / PNG / WEBP
  - Max Size: 5MB
  - Aspect Ratio: 1:1 or 16:9 or 4:5

X Feed Post (Video):
  - Format: MP4 / MOV
  - Max Size: 512MB
  - Max Duration: 2:20 (140 seconds)
  - Min Resolution: 1280×720
```

## Authentication Model

- **Deferred Auth:** Users can enter caption and preview before connecting X account
- **OAuth 2.0 PKCE:** Initiated via `auth.initOauth()` inside the intent
- **Token Storage:** Server-side only, encrypted at rest — never in frontend
- **Multi-Account:** Users can connect multiple X accounts; selector shows handle + avatar
- **Disconnection:** App removal triggers server-side token deletion
- **Per-Team Auth:** Each Canva team requires independent X authentication

## Implementation Notes

- App scaffolded with `npx @canva/cli create --template content-publisher`
- All UI via `@canva/app-ui-kit` — no third-party component libraries
- Must support Canva light and dark mode
- Must be internationalisation-ready (all strings externalised)
- Responsive across desktop and tablet surfaces
- Post-publish response must include `externalUrl` linking to live X post

---
