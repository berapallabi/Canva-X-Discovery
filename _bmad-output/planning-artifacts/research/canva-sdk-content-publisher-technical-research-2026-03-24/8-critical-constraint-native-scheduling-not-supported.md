# 8. ⚠️ Critical Constraint: Native Scheduling NOT Supported

> **"The Content Publisher intent doesn't support Canva native scheduling yet, so you need to provide a way for the user to input the timing to schedule on your platform."**
> — Canva Design Guidelines, Content Publisher

**Implication for the PRD:**
- The app **must implement its own scheduling backend** (server-side queue or X API-backed scheduling)
- The scheduling UI (date/time picker, timezone display) must be built in the **App Settings UI** section
- The preview should reflect the scheduled time if possible
- Always display the **timezone** explicitly alongside the date/time picker
- Default to the user's local timezone with ability to change

---
