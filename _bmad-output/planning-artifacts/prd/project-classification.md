# Project Classification

| Attribute | Value |
|---|---|
| **Project Type** | Canva Marketplace App (Content Publisher intent — Canva Apps SDK) |
| **Domain** | Social Media Publishing |
| **Complexity** | Medium — dual-API dependency (Canva SDK + X API v2), custom scheduling backend, OAuth lifecycle management, media validation |
| **Project Context** | Greenfield |
| **Platform Dependency** | Canva Apps SDK (`prepareContentPublisher`, `@canva/user`, App UI Kit) |
| **External Integration** | X API v2 (`POST /2/tweets`, `POST /2/media/upload`) |
| **Required Scope** | `canva:design:content:read` + X OAuth 2.0 (`tweet.write`, `media.write`, `offline.access`) |

---
