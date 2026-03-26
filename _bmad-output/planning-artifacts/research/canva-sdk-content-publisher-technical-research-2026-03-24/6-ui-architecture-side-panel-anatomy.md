# 6. UI Architecture (Side Panel Anatomy)

The right-hand side panel is split into two areas:

## A. Canva-Managed UI (top — developer cannot modify)
- **Minified preview** — collapsed preview with expand toggle
- **Output type selector** — dropdown for format (e.g., "X Feed Post", "X Video Post")
- **Page selector** — pick which Canva pages/slides to export

## B. App Settings UI (bottom — developer-owned)
Must implement using **App UI Kit** components only. Recommended structure:
1. Account indicator (X handle + avatar)
2. Caption text area (character counter: 280 / 25,000 premium)
3. Scheduling toggle (date/time picker) — _must be implemented by the app, not Canva_
4. Any additional X-specific settings (e.g., reply permissions)

> ⚠️ **Do NOT add a custom "Publish" button** — the Publish button is managed entirely by Canva.

---
