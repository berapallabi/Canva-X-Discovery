# Visual User Journey Flowcharts

This document provides visual state-machine representations of the textual click-map found in [`user-journeys.md`](./user-journeys.md). It is designed to visually map every constraint, validation boundary, and network exception for the frontend engineering team.

## 1. End-to-End Integration Flow (Happy Path + Exhaustive Exceptions)

This diagram tracks the UI interactions from the Canva Share Menu through to the final X API Network responses, including all explicit HTTP 4xx/5xx handling.

```mermaid
graph TD
    classDef uiState fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef action fill:#d4e6f1,stroke:#2980b9,stroke-width:2px;
    classDef error fill:#f5b7b1,stroke:#c0392b,stroke-width:2px;
    classDef success fill:#d5f5e3,stroke:#27ae60,stroke-width:2px;
    classDef backend fill:#fcf3cf,stroke:#f1c40f,stroke-width:2px;

    %% Phase 1 & 2: Entry & Auth
    A([User finishes Canva Design]) --> B[Clicks Share > X App Icon]:::action
    B --> C{auth.getTokens Check}:::backend
    
    C -->|No Tokens| D[Phase 2A: Unauthenticated UI]:::uiState
    D --> E[User clicks 'Connect to X']:::action
    E --> F[OAuth 2.0 Popup]:::uiState
    F -->|Deny| D
    F -->|Success: Tokens Saved| G
    
    C -->|Valid Tokens| G[Phase 3: Authenticated Settings UI]:::uiState
    
    %% Phase 3: Field Interactions & Validations
    G --> H[Field 3.1: Account Selector]:::action
    H -.->|Change Context| G
    H -.->|Disconnect Clicked| H1[Canva Token DELETE]:::backend
    H1 --> D
    
    G --> I[Field 3.2: Media Selection]:::action
    I --> I1{Validation Gate}
    I1 -->|>4 Images| I2[UI Block: 'Max 4 Images']:::error
    I1 -->|Mixed Media| I3[UI Block: 'Cannot Mix Video/Image']:::error
    I1 -->|>5MB or >140s| I4[UI Block: 'Size/Duration Exceeded']:::error
    I1 -.->|Pass| J
    
    G --> J[Field 3.3: Caption Input]:::action
    J --> J1{Length Check}
    J1 -->|>280 Chars| J2[UI Block: Red Counter, Publish Disabled]:::error
    J1 -.->|Pass| K
    
    G --> K[Field 3.5: Advanced Options]:::action
    K --> K1[Typeahead User Tags & Alt Text]
    K1 -->|>10 Tags| K2[UI Block: 'Max 10 Tags Toast']:::error
    
    %% Phase 4 & 5: Preview and Publish
    I1 & J1 & K1 == Real-Time Sync ==> L[Phase 4: WYSIWYG Live Preview Panel Renders]:::uiState
    
    J -.->|Valid Form Payload| M([Phase 5: User clicks 'Publish to X']):::action
    
    M --> N[Canva SDK publishContent fired]:::backend
    N --> O[Next.js Proxy Receives POST]:::backend
    
    %% Backend Validations & Responses
    O --> P{Verify Canva JWT Signature}:::backend
    P -->|HTTP 401 Invalid Sign| P1[UI Purge Tokens: 'Session Expired']:::error
    P1 --> D
    
    P -->|Valid| Q[React Proxy Chunker initialized]:::backend
    Q --> R[X API /media/upload INIT + APPEND + FINALIZE]:::backend
    
    R --> S[X API /tweets POST with Media ID]:::backend
    
    S -->|HTTP 502/503| S1[UI Halt: 'X API Downtime']:::error
    S -->|HTTP 429| S2[UI Halt: 'Rate Limit Exhausted Reset Epoch']:::error
    S -->|HTTP 403 Suspended| P1
    S -->|HTTP 200 Success| T([Phase 6: Success Screen 'View on X' Link]):::success
```

## 2. Dynamic Thread Builder Validation UX

This sub-diagram zooms into the UI logic for Field 3.4 (`+ Add another tweet`), mapping exactly how the UI prevents users from breaking X's maximum thread depth limit natively within Canva.

```mermaid
graph LR
    classDef uiState fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef action fill:#d4e6f1,stroke:#2980b9,stroke-width:2px;
    classDef error fill:#f5b7b1,stroke:#c0392b,stroke-width:2px;

    Start([User clicks '+ Add another tweet']) --> Eval{Count Current Blocks}
    
    Eval -->|Count < 25| Render[UI: Render new Media + Caption block below]:::uiState
    Render --> Update[UI: Append vertical thread-line SVG across blocks]:::uiState
    Update --> Pre[WYSIWYG: Render connected thread visually]:::uiState
    Pre --> Await[Await Next User Action]
    
    Eval -->|Count = 25| Max[UI: Hide '+ Add another tweet' Button]:::error
    Max --> Warn[UI: Display Toast 'Maximum thread length of 25 reached']:::error
```
