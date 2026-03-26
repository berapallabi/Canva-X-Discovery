# Starter Template Evaluation

## Primary Technology Domain
Frontend-heavy Platform Extension (Canva App SDK Frontend + Stateless Serverless Proxy Backend).

## Selected Starter: Canva CLI Content Publisher + Next.js (API Routes)

**Rationale for Selection:**
The Canva CLI is strictly required for the App UI Kit and SDK types. Since we pivoted to the "Zero Database" architecture (Canva handles OAuth statefully), our backend is simply a stateless proxy to handle X API requests (because X API lacks CORS). Next.js API Routes are the industry standard for lightweight, zero-maintenance stateless proxies.

**Initialization Command:**
```bash
# Frontend
npx @canva/cli create --template "content_publisher" my-canva-app

# Backend Proxy
npx create-next-app@latest my-stateless-proxy --typescript --eslint --no-src-dir --no-tailwind --app
```

**Architectural Decisions Provided by Starter:**
- **Language:** TypeScript 
- **Database:** None (Canva Native OAuth used).
- **Styling:** Canva App UI Kit (Frontend).
- **Code Organization:** Frontend grouped by intent UI. Backend is pure Serverless functions (`app/api/publish/route.ts`).
- **Development Experience:** Hot-reloading via `npm run dev`. Localhost tunneling (e.g., Ngrok) required.
