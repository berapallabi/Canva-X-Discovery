# 7. Preview UI Requirements

The Preview UI renders in a **separate iframe** from the Settings UI:
- Must update in **real time** as settings change
- Must handle all states: loading, ready, error
- For **images**: show placeholder while loading, smooth transition when ready
- For **videos**: show progress bar (NOT a spinner), start with thumbnail, upgrade to full video on demand
- Must reflect dark/light mode toggle
- Post-publish: provide a URL that links to the live X post (becomes the primary CTA in Canva's post-publish dialog)

---
