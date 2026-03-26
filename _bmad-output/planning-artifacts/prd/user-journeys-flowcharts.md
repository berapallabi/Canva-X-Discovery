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

    %% Phase 1 & 2: Entry & Routing
    A([User clicks Share > X App Icon]) --> B{auth.getTokens Check}:::backend
    
    B -->|First-Time User| C[Phase 2A: Static Onboarding Page]:::uiState
    C --> D[User clicks 'Open']:::action
    D --> E[Phase 2B: Zero-State Settings UI]:::uiState
    
    B -->|Logged Out| E
    
    E --> F[User drafts Media & Caption in Live Preview]:::action
    F --> G[User clicks 'Connect to X to Publish']:::action
    
    G --> H[OAuth 2.0 Popup]:::uiState
    H -->|Denial or Close| E
    H -->|Success: Tokens Saved| I
    
    B -->|Valid Tokens| I[Phase 3: Authenticated Settings UI]:::uiState
    
    %% Phase 3: Field Interactions & Validations
    I --> J[Field 3.1: Account Selector]:::action
    J -.->|Triggers Premium Cap Check| I
    J -.->|Disconnect Clicked| J1[Canva Token DELETE]:::backend
    J1 --> E
    
    I --> K[Field 3.2: Media Selection]:::action
    K --> K1{Validation Gate}
    K1 -->|>4 Images| K2[UI Block: 'Max 4 Images']:::error
    K1 -->|Mixed Media| K3[UI Block: 'Cannot Mix Video/Image']:::error
    K1 -->|>5MB or >140s| K4[UI Block: 'Size/Duration Exceeded']:::error
    K1 -.->|Pass| L
    
    I --> L[Field 3.3: Caption Input]:::action
    L --> L1{Length Check}
    L1 -->|>280 Chars Base| L2[UI Block: Red Counter, Publish Disabled]:::error
    L1 -.->|Pass| M
    
    I --> M[Field 3.5: Advanced Options]:::action
    M --> M1[Typeahead User Tags]
    M1 -->|>10 Tags| M2[UI Block: 'Max 10 Tags Toast']:::error
    
    %% Phase 4 & 5: Complex Preview and Publish
    K1 -.->|Calculates 2x2 Grids or Video UI| N[Phase 4: WYSIWYG Live Preview Renders]:::uiState
    L1 -.-> N
    M1 -.-> N
    M -.->|Inject Thread Lines / Blur Sensitive| N
    
    L -.->|Valid Form Payload| O([Phase 5: User clicks 'Publish to X']):::action
    
    O --> P[Canva SDK publishContent fired]:::backend
    P --> Q[Next.js Proxy Receives POST]:::backend
    
    %% Backend Validations & Responses
    Q --> R{Verify Canva JWT Signature}:::backend
    R -->|HTTP 401 Invalid Sign| R1[UI Purge Tokens: 'Session Expired']:::error
    R1 --> C
    
    R -->|Valid| S[React Proxy Chunker initialized]:::backend
    S --> T[X API /media/upload INIT + APPEND + FINALIZE]:::backend
    
    T --> U[X API /tweets POST with Media ID]:::backend
    
    U -->|HTTP 502/503| U1[UI Halt: 'X API Downtime']:::error
    U -->|HTTP 429| U2[UI Halt: 'Rate Limit Exhausted Epoch']:::error
    U -->|HTTP 403 Suspended| R1
    U -->|HTTP 200 Success| V([Phase 6: Success Screen 'View on X' Link]):::success
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
