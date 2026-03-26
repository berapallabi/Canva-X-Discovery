# 3. Key Data Primitives

| Primitive | Description | Limit |
|---|---|---|
| **OutputType** | Defines the media format Canva should export (e.g., "X Feed Post" = JPG/PNG, <5MB, 1:1 or 16:9 aspect ratio) | Configurable per app |
| **PublishRef** | Opaque JSON string carrying all settings between steps (caption, account, schedule time) | Max **5 KB** |
| **PreviewMedia** | Real-time file previews shown in the preview iframe; updates dynamically as settings change | Starts as thumbnail; upgrades to full video on demand |
| **OutputMedia** | Final production-ready exported files from Canva; passed to `publishContent` | Matches OutputType specs |

---
