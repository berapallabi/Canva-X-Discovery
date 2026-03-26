# 2. Core Architecture — 4 Required Functions

All Content Publisher apps must implement exactly **4 functions** via `prepareContentPublisher()`:

| Function | Role | Notes |
|---|---|---|
| `getPublishConfiguration` | Defines Output Types (media formats/specs the app accepts) | Called at initialization |
| `renderSettingsUi` | Renders the app's side panel — caption, account selector, schedule picker | Must use App UI Kit components |
| `renderPreviewUi` | Renders the live X feed preview in a separate iframe | Updates in real-time via `registerOnPreviewChange` |
| `publishContent` | Handles media upload + post creation; called when user clicks Publish | Calls X API; returns `{status, externalId, externalUrl}` |

## Full Registration Pattern

```typescript
prepareContentPublisher({
  getPublishConfiguration,
  renderSettingsUi: ({ updatePublishSettings, registerOnSettingsUiContextChange }) => { ... },
  renderPreviewUi: ({ registerOnPreviewChange }) => { ... },
  publishContent,
});
```

---
