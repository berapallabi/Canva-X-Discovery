# 5. Authentication Architecture

## Deferred Authentication (Required Pattern)

Canva's design guidelines **require** deferred authentication for Content Publisher apps:
- Users MUST be able to see and interact with the Settings UI and Preview **before** connecting their X account
- Only at publish time (or when required) should authentication be triggered
- The `validityState` field controls the Publish button visibility:
  - `"invalid_authentication_required"` → hides the Publish button (shows Connect prompt)
  - `"invalid_missing_required_fields"` → hides Publish button (required fields incomplete)
  - `"valid"` → enables the Publish button

## Auth Implementation

```typescript
import { auth } from "@canva/user";
const oauth = auth.initOauth();

// Check token
const tokenResponse = await oauth.getAccessToken({ scope });

// Trigger auth flow
const response = await oauth.requestAuthorization({ scope });
```

## Auth UX Rules (from Canva design guidelines)
- Auth flows happen in a **pop-up window**, NOT inside the app iframe
- Use `requestAuthentication` method — don't bypass the standard flow
- Show X account **handle** (not email) as the active account indicator
- Display account avatar/profile picture when available
- Support multi-account: allow users to switch between connected X accounts
- **Disconnection handling**: when a user removes the app, delete the OAuth token; require re-auth on reconnect

---
