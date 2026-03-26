# Implementation Patterns & Consistency Rules

## Naming Patterns

**API Naming Conventions:**
- Proxy endpoints inside Next.js use standard REST action names (e.g., `/api/publish/tweet`, `/api/publish/media`).

**Code Naming Conventions:**
- React Components use `PascalCase` (e.g., `AccountSelector.tsx`).
- React Hooks use camelCase `usePascalCase` (e.g., `useXAccount.ts`).
- Serverless Route handlers strictly map to their domain actions.

## Structure Patterns

**Project Organization:**
- Feature-based grouping: `src/features/Settings/` and `src/features/Preview/`.
- Shared UI components in `src/shared/components/`.
- Shared Canva SDK hooks in `src/shared/hooks/`.

**File Structure Patterns:**
- Logic explicitly isolating X API calls into an external module `lib/services/x-api.ts` vs the Next.js Route interface `app/api/publish/route.ts`.

## Format Patterns

**API Response Formats:**
All Next.js API route responses MUST follow the strict JSON wrapper:
`{ success: boolean, data?: Payload, error?: { code: string, message: string } }`

## Enforcement Guidelines

**All AI Agents MUST:**
- Never leak state between `renderSettingsUi` and `renderPreviewUi` since they render independently.
- Always unwrap the strictly typed API Response Format before evaluating HTTP responses in the frontend.
- Mock all `lib/services/x-api.ts` functions during testing; do not perform live Next.js boots for unit testing.
