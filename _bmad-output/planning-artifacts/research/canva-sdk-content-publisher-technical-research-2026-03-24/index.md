# Canva Apps SDK — Content Publisher Technical Research

## Table of Contents

- [Canva Apps SDK — Content Publisher Technical Research](#table-of-contents)
  - [1. The Content Publisher Intent](./1-the-content-publisher-intent.md)
  - [2. Core Architecture — 4 Required Functions](./2-core-architecture-4-required-functions.md)
    - [Full Registration Pattern](./2-core-architecture-4-required-functions.md#full-registration-pattern)
  - [3. Key Data Primitives](./3-key-data-primitives.md)
  - [4. Required OAuth Scope](./4-required-oauth-scope.md)
  - [5. Authentication Architecture](./5-authentication-architecture.md)
    - [Deferred Authentication (Required Pattern)](./5-authentication-architecture.md#deferred-authentication-required-pattern)
    - [Auth Implementation](./5-authentication-architecture.md#auth-implementation)
    - [Auth UX Rules (from Canva design guidelines)](./5-authentication-architecture.md#auth-ux-rules-from-canva-design-guidelines)
  - [6. UI Architecture (Side Panel Anatomy)](./6-ui-architecture-side-panel-anatomy.md)
    - [A. Canva-Managed UI (top — developer cannot modify)](./6-ui-architecture-side-panel-anatomy.md#a-canva-managed-ui-top-developer-cannot-modify)
    - [B. App Settings UI (bottom — developer-owned)](./6-ui-architecture-side-panel-anatomy.md#b-app-settings-ui-bottom-developer-owned)
  - [7. Preview UI Requirements](./7-preview-ui-requirements.md)
  - [8. ⚠️ Critical Constraint: Native Scheduling NOT Supported](./8-critical-constraint-native-scheduling-not-supported.md)
  - [9. Error Handling Requirements](./9-error-handling-requirements.md)
    - [In :](./9-error-handling-requirements.md#in)
    - [Auth errors (minimum required):](./9-error-handling-requirements.md#auth-errors-minimum-required)
  - [10. Canva Marketplace Submission Requirements](./10-canva-marketplace-submission-requirements.md)
  - [11. Relevant API References](./11-relevant-api-references.md)
