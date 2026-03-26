# Architecture Diagrams

## System Component Diagram
This diagram illustrates our "Zero Database" stateless proxy architecture.

```mermaid
graph TD
    subgraph "Canva Platform"
        E["Canva Editor"] -->|iframe| CF["canva-frontend (React App)"]
        CF -->|Stores X Tokens| CA["Canva Native OAuth"]
    end
    
    subgraph "Vercel Serverless"
        CF -->|HTTP POST JSON| NP["Next.js Proxy (/api/publish)"]
        NP --> JV{"Canva JWT Verifier"}
        JV -->|Invalid| ERR["Reject 401"]
        JV -->|Valid| MH["Media Uploader (Chunked)"]
        JV -->|Valid| TH["Tweet Controller"]
    end
    
    subgraph "X Platform"
        MH -->|INIT/APPEND/FINALIZE| XMA["X Media API v1.1"]
        TH -->|Post| XTA["X Tweets API v2"]
    end
    
    style NP fill:#f9f,stroke:#333,stroke-width:2px
    style CF fill:#bbf,stroke:#333,stroke-width:2px
```

## Request Sequence Diagram
This diagram maps the standard execution flow during a "Publish to X" event.

```mermaid
sequenceDiagram
    participant User
    participant CanvaApp as Canva App (React)
    participant NextJS as Next.js Proxy (Serverless)
    participant XAPI as X API

    User->>CanvaApp: Click "Publish to X"
    CanvaApp->>NextJS: POST /api/publish (JWT, Media Buffer, Caption)
    
    rect rgb(240, 240, 240)
        Note over NextJS: Authentication Layer
        NextJS->>NextJS: Cryptographically Verify Canva JWT Signature
    end
    
    rect rgb(230, 245, 255)
        Note over NextJS,XAPI: Media Processing Layer (Chunked Upload)
        NextJS->>XAPI: POST /1.1/media/upload?command=INIT
        XAPI-->>NextJS: media_id
        NextJS->>XAPI: POST /1.1/media/upload?command=APPEND
        XAPI-->>NextJS: success
        NextJS->>XAPI: POST /1.1/media/upload?command=FINALIZE
        XAPI-->>NextJS: media_id ready
    end
    
    Note over NextJS,XAPI: Publishing Layer
    NextJS->>XAPI: POST /2/tweets (caption, media_id)
    XAPI-->>NextJS: success (Tweet ID)
    
    NextJS-->>CanvaApp: 200 OK {success: true, data: "..."}
    CanvaApp-->>User: Show Success Notification
```
