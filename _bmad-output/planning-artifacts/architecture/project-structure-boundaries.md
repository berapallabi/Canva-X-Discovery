# Project Structure & Boundaries

## Complete Project Directory Structure

```text
canva-x-publisher/
├── canva-frontend/                 # Canva App SDK (React)
│   ├── package.json
│   ├── tsconfig.json
│   ├── .env.example
│   ├── src/
│   │   ├── index.tsx               # App entry point (prepareContentPublisher)
│   │   ├── features/
│   │   │   ├── Settings/           # Caption editor, Account selector
│   │   │   │   ├── SettingsUI.tsx
│   │   │   │   └── useSettings.ts
│   │   │   └── Preview/            # WYSIWYG X Feed simulation
│   │   │       ├── PreviewUI.tsx
│   │   │       ├── XCard.tsx
│   │   │       └── usePreview.ts
│   │   ├── shared/
│   │   │   ├── components/         # Shared UI Kit wrappers
│   │   │   ├── hooks/              # useCanvaAuth hook
│   │   │   └── utils/              # 5KB Payload pre-flight validators
│   │   └── types/                  # X API and Canva SDK interfaces
│   └── tests/
│       ├── features/
│       └── shared/
│
└── nextjs-proxy/                   # Stateless Serverless Backend
    ├── package.json
    ├── next.config.js
    ├── tsconfig.json
    ├── .env.local                  # Canva App Secret, X API Keys
    ├── src/
    │   ├── app/
    │   │   └── api/
    │   │       └── publish/
    │   │           └── route.ts    # Main upload proxy
    │   ├── lib/
    │   │   ├── canva/
    │   │   │   └── jwt.ts          # Canva JWKS Signature verification
    │   │   └── services/
    │   │       └── x-api/
    │   │           ├── client.ts   # X API Core Fetcher
    │   │           └── media.ts    # Chunked INIT/APPEND/FINALIZE logic
    │   └── types/
    └── tests/
        ├── api/                      # Integration tests for route
        └── services/                 # X API Unit tests isolated from Next.js
```

## Architectural Boundaries

**API Boundaries:**
- The Next.js `/api/publish` boundary acts as the strict demarcation between the Canva context (Frontend) and the X context (External API). It enforces strict payload parsing and Canva JWT cryptographic authentication before executing upstream requests to X.

**Component Boundaries:**
- `SettingsUI` and `PreviewUI` communicate exclusively via Canva's native `setContent` and `registerOnPublishConfigChange` callback methods. No global state management (like Redux or Context) wraps the two separate React roots, matching the exact runtime environment of the Canva side panel iframe.

**Service Boundaries:**
- `lib/services/x-api/` has absolute zero dependencies on Next.js Request or Response objects. It only accepts standard TypeScript primitives (`FileBuffer`, `Tokens`, strings), ensuring the proxy logic remains completely pure, independently testable, and reusable if we ever move off Next.js.

## Requirements to Structure Mapping

**Epic/Feature Mapping:**
- **FR4: WYSIWYG X Preview:** `canva-frontend/src/features/Preview/XCard.tsx`
- **FR5: X Media Pre-flight:** `canva-frontend/src/shared/utils/validators.ts`
- **FR18/FR19: Chunked Media Upload:** `nextjs-proxy/src/lib/services/x-api/media.ts`
- **NFR10: JWT Verification:** `nextjs-proxy/src/lib/canva/jwt.ts`
