# X Platform: Media Preview Layout Research

> **Purpose:** Reference document for implementing Story 3.1 (Simulator Scaffolding) and Story 3.2 (Asymmetrical Grid Crops) in Epic 3. This file documents the exact X feed rendering behavior across all **in-scope media combinations: Images and Videos only.**

> **Scope Note:** GIF and Poll formats are **out of scope** for this project. All scenarios in this document cover images (PNG/JPG) and videos (MP4) only.

---

## 1. The Golden Rule: X's Container

All media in the X feed renders inside a fixed **aspect-ratio container** of approximately **16:9** on desktop and adapts to taller ratios on mobile. The content inside this container is **center-cropped** to fit rather than letter-boxed (black bars). This is the primary source of the "safe zone" constraint.

> **Simulator Implementation Note:** Our WYSIWYG preview in Epic 3 must replicate this container using a fixed `aspect-ratio: 16 / 9` CSS rule on the outer `div`.

---

## 2. Image Layouts (Static)

### 2A. Single Image

**Container behavior:** Renders at its native aspect ratio up to a capped maximum.

| Canva Export Ratio | X Feed Behavior | Visible Result |
| :--- | :--- | :--- |
| **16:9** (Landscape) | Renders as-is, full width. No cropping. | ✅ Best result |
| **1:1** (Square) | Renders as-is with top/bottom letterbox padding. | ✅ Clean |
| **4:5** (Portrait) | Renders as-is. Slightly taller in feed. | ✅ Good |
| **9:16** (Tall Portrait) | Cropped to approximately a 4:5 window by X. Top & bottom are cut. | ⚠️ Safe zone required |
| **3:1** (Ultra-wide) | Maximum width, small height. Feels "banner-like". | ⚠️ May look thin |

**ASCII Layout:**
```
┌─────────────────────────┐
│                         │
│       SINGLE IMAGE      │
│       (Full Width)      │
│                         │
└─────────────────────────┘
```

---

### 2B. Two Images (Side by Side)

**Container behavior:** Both images share a **50/50 horizontal split**. Each image is forced into a **7:8 (≈ 0.875) aspect ratio** regardless of its native dimensions. This is one of the most aggressively cropped layouts.

| Canva Export Ratio | X Feed Behavior | Safe Zone Needed? |
| :--- | :--- | :--- |
| **1:1** (Square) | Slight top/bottom crop to enforce 7:8. | Minor |
| **4:5** (Portrait) | Good fit for 7:8. Minimal cropping. | ✅ Minimal |
| **16:9** (Landscape) | Heavily cropped top & bottom. Only centre shows. | ⚠️ Heavy |
| **9:16** (Tall Portrait) | Cropped to 7:8 window — large top/bottom cut. | ❌ Avoid |

**ASCII Layout:**
```
┌───────────┬───────────┐
│           │           │
│  IMAGE 1  │  IMAGE 2  │  ← Each: forced to 7:8 ratio
│(7:8 crop) │(7:8 crop) │
│           │           │
└───────────┴───────────┘
```

---

### 2C. Three Images (1 Large + 2 Stacked)

**Container behavior:** The layout is **asymmetrical by design**. The first selected image is placed in a large left column at **7:8** ratio. The second and third images are stacked vertically in the right column, each at a tight **4:7 (≈ 0.57) aspect ratio** — these are very aggressively cropped to a narrow tall window.

| Column | Image | Forced Ratio | Cropping Severity |
| :--- | :--- | :--- | :--- |
| Left (dominates) | Image 1 | **7:8** | Moderate |
| Right Top | Image 2 | **4:7** | High — wide images lose sides |
| Right Bottom | Image 3 | **4:7** | High — wide images lose sides |

**ASCII Layout:**
```
┌───────────┬──────┐
│           │  2   │  ← 4:7 ratio, narrow
│  IMAGE 1  ├──────┤
│  (7:8)    │  3   │  ← 4:7 ratio, narrow
└───────────┴──────┘
```

> **Simulator Constraint:** The right-column images should occupy exactly 50% of the container width but only 50% of the container height each.

---

### 2D. Four Images (2×2 Grid)

**Container behavior:** A balanced **2×2 symmetric grid**. Each of the 4 images is cropped to roughly a **1:1** box. Consistent centering is applied.

| Canva Export Ratio | X Feed Behavior | Quality |
| :--- | :--- | :--- |
| **1:1** (Square) | Near-perfect fit. Very predictable. | ✅ Best |
| **4:5** (Portrait) | Slight side crop on wider devices. | ✅ Good |
| **16:9** (Landscape) | Significant top/bottom crop. Only centre strip visible. | ⚠️ Use safe zone |
| **9:16** (Tall Portrait) | Extreme crop. Only a thin centre band shows. | ❌ Avoid |

**ASCII Layout:**
```
┌───────────┬───────────┐
│  IMAGE 1  │  IMAGE 2  │  ← Top row: ~1:1 per cell
├───────────┼───────────┤
│  IMAGE 3  │  IMAGE 4  │  ← Bottom row: ~1:1 per cell
└───────────┴───────────┘
```

---

## 3. Video Layouts (MP4)

Videos can be uploaded as a **standalone item** or **mixed with images** in the same post (up to 4 total media items). See Section 5 for all mixed image + video grid layouts.

### 3A. Single Video

| Source Ratio | X Feed Behavior | Notes |
| :--- | :--- | :--- |
| **16:9** (Landscape) | Renders full width, no crop. Player controls overlay at bottom. | ✅ Optimal |
| **1:1** (Square) | Renders as a square block with padding on sides (desktop) or full-width (mobile). | ✅ Good |
| **4:5** (Portrait) | Taller feed card. Works well on mobile. | ✅ Recommended for mobile-first |
| **9:16** (Tall Portrait) | Cropped to approximately 4:5 in feed. Top/bottom of video is cut. Full version in the video tab. | ⚠️ Safe zone top/bottom |

**In-feed Video ASCII (16:9):**
```
┌─────────────────────────┐
│                         │
│  ▶  VIDEO PREVIEW       │
│     (Full Width 16:9)   │
│                         │
│ [0:34]          [  ∞ ]  │  ← Player controls
└─────────────────────────┘
```

**Important Distinction:**
- **Main Feed:** Often clips tall video; shows landscape or square framing.
- **Video Tab / Media Tab:** Shows the full video at its native ratio (including 9:16).

---

## 4. Mixed Media Layouts (Image + Video)

> **Platform Rule:** X supports up to **4 media items per post**, freely mixing images and videos. The **same positional grid rules** from Section 2 apply — the grid slot count determines the layout, not the media type. Each video cell receives a `▶` play button overlay on its thumbnail.

> **Key UX Rule:** The **user's upload sequence determines slot assignment** — item 1 always takes the dominant/primary position. The Canva Extension must allow **drag-to-reorder** before posting, since slot position directly affects which item gets the large-left treatment vs. the narrower crop.

---

### 4A. 1 Video + 1 Image (2 Items)

**Container behavior:** Identical to the 2-image layout — **50/50 horizontal split**, each cell forced to **7:8** aspect ratio. The video cell shows a centre-aligned `▶` play button overlay on its thumbnail.

| Slot | Media Type | Forced Ratio | Overlay |
| :--- | :--- | :--- | :--- |
| Left (Item 1) | Video (thumbnail) | 7:8 — centre crop | `▶` play button |
| Right (Item 2) | Image | 7:8 — centre crop | `ALT` if set |

**ASCII Layout:**
```
┌───────────┬───────────┐
│  ▶        │           │
│  VIDEO    │  IMAGE    │  ← Each: forced to 7:8 ratio
│(thumbnail)│           │
│           │           │
└───────────┴───────────┘
```

> **Simulator Note:** Render the `▶` badge centred over the video thumbnail cell. If image is item 1, it occupies the left slot and video moves to the right.

---

### 4B. 1 Video + 2 Images (3 Items)

**Container behavior:** Matches the asymmetric 3-item layout from Section 2C. The **first uploaded item occupies the large left column** (7:8). The two remaining items stack in the right column (each at 4:7 ratio). Overlay badges appear per-cell.

**Scenario 1 — Video in dominant left slot (Item 1 = Video):**

| Slot | Media Type | Forced Ratio | Overlay |
| :--- | :--- | :--- | :--- |
| Left (large) | Video | 7:8 | `▶` play button (centred) |
| Right Top | Image | 4:7 | `ALT` if set |
| Right Bottom | Image | 4:7 | `ALT` if set |

```
┌───────────┬──────┐
│  ▶        │  2   │  ← Image, 4:7 ratio
│  VIDEO    ├──────┤
│  (7:8)    │  3   │  ← Image, 4:7 ratio
└───────────┴──────┘
```

**Scenario 2 — Video in a right narrow slot (Item 2 or 3 = Video):**

> ⚠️ High-crop scenario — the 4:7 forced ratio cuts heavy left & right from landscape video. Warn the user that wide-format videos lose significant side content in this position.

```
┌───────────┬──────┐
│           │ ▶ 2  │  ← Video in right top cell (4:7, very narrow)
│  IMAGE 1  ├──────┤
│  (7:8)    │  3   │  ← Image
└───────────┴──────┘
```

---

### 4C. 2 Videos + 1 Image (3 Items)

**Container behavior:** Same asymmetric layout. First item = large left column. The two video thumbnails each receive a `▶` overlay in the right column.

**Scenario 1 — Image in dominant left slot (Item 1 = Image):**

| Slot | Media Type | Forced Ratio | Overlay |
| :--- | :--- | :--- | :--- |
| Left (large) | Image | 7:8 | `ALT` if set |
| Right Top | Video | 4:7 | `▶` play button |
| Right Bottom | Video | 4:7 | `▶` play button |

```
┌───────────┬──────┐
│           │ ▶ 2  │  ← Video thumbnail, 4:7 ratio
│  IMAGE 1  ├──────┤
│  (7:8)    │ ▶ 3  │  ← Video thumbnail, 4:7 ratio
└───────────┴──────┘
```

**Scenario 2 — Video in dominant left slot (Item 1 = Video):**

```
┌───────────┬──────┐
│  ▶        │  2   │  ← Image, 4:7 ratio
│  VIDEO 1  ├──────┤
│  (7:8)    │ ▶ 3  │  ← Video thumbnail, 4:7 ratio
└───────────┴──────┘
```

---

### 4D. Mixed 4 Items — Images + Videos (2×2 Grid)

**Container behavior:** Any combination of 4 images and/or videos renders in a **2×2 symmetric grid**. Each cell is cropped to approximately **1:1**. Each video cell receives a `▶` play button overlay.

| Slot | Overlay Applied |
| :--- | :--- |
| Image cell | `ALT` badge if alt-text set |
| Video cell | `▶` play button (centred) |

**Example: 3 Images + 1 Video (4 Items):**
```
┌───────────┬───────────┐
│  IMAGE 1  │  IMAGE 2  │  ← Top row: ~1:1 per cell
├───────────┼───────────┤
│  IMAGE 3  │  ▶ VIDEO  │  ← Video thumbnail with play badge
└───────────┴───────────┘
```

**Example: 2 Images + 2 Videos (4 Items):**
```
┌───────────┬───────────┐
│  IMAGE 1  │  ▶ VIDEO 1│
├───────────┼───────────┤
│  IMAGE 2  │  ▶ VIDEO 2│
└───────────┴───────────┘
```

> **Safe Zone Note:** In the 2×2 grid, all cells are ~1:1. Wide videos (16:9) lose significant top/bottom area. Recommend **1:1 or 4:5** sources for video in a 4-item post.

---

### 4E. Slot Ordering Reference

The user's upload sequence determines slot assignment. Item 1 always takes the dominant position:

| Total Items | Item 1 | Item 2 | Item 3 | Item 4 |
| :---: | :--- | :--- | :--- | :--- |
| 2 | Left (50%, 7:8) | Right (50%, 7:8) | — | — |
| 3 | Left large (7:8) | Right top (4:7) | Right bottom (4:7) | — |
| 4 | Top-left (1:1) | Top-right (1:1) | Bottom-left (1:1) | Bottom-right (1:1) |

> **Simulator Implementation Note (Story 3.2):** The simulator must re-render the grid live whenever the user reorders items, so they can see how slot position affects cropping before they post.

---

## 5. Simulator Mapping Summary

This table summarizes what our **Epic 3 WYSIWYG Simulator** must render per in-scope combination:

| Media Types | Count | Grid Template | Crop Ratios | Overlay Badge |
| :--- | :---: | :--- | :--- | :--- |
| Image only | 1 | Single full-width block | Native (capped) | `ALT` if alt-text exists |
| Image only | 2 | 50/50 horizontal split | 7:8 each | `ALT` per image |
| Image only | 3 | Left large + Right 2-stack | Left: 7:8 / Right: 4:7 each | `ALT` per image |
| Image only | 4 | 2×2 grid | ~1:1 each | `ALT` per image |
| Video only | 1 | Single full-width block | 16:9 (or native) | `▶` play button |
| **1 Video + 1 Image** | **2** | **50/50 split** | **7:8 each** | **`▶` on video cell** |
| **1 Video + 2 Images** | **3** | **Left large + Right 2-stack** | **Left: 7:8 / Right: 4:7 each** | **`▶` per video cell** |
| **2 Videos + 1 Image** | **3** | **Left large + Right 2-stack** | **Left: 7:8 / Right: 4:7 each** | **`▶` on each video cell** |
| **Images + Videos** | **4** | **2×2 grid** | **~1:1 each** | **`▶` on each video cell** |

---

## 6. Canva Design Ratio Recommendations

Given the above grid behaviors, here is the guidance we surface to the user **inside the Settings Panel** (Story 2.2) when they select a format:

| User Intent | Best Canva Design Ratio | Why |
| :--- | :--- | :--- |
| Single Image | **16:9** or **1:1** | No crop; safe for all preview types. |
| 2 Images | **4:5** | Closest match to the forced 7:8 grid cell. |
| 3 Images (left slot) | **4:5** | Closest match to the 7:8 left-column. |
| 3 Images (right slots) | **9:16** or taller | Closest to the very narrow 4:7 right cells. |
| 4 Images | **1:1** | Square grid cells; most predictable result. |
| Video (standalone) | **16:9** or **4:5** | Best feed compatibility; minimal crop. |
| Video (2-item post, left slot) | **4:5** | Matches 7:8 forced ratio with minimal crop. |
| Video (3-item post, right slot) | **4:5 or taller** | Matches narrow 4:7 crop — wide video loses sides. |
| Video (4-item post) | **1:1** | Square cells; predictable crop for thumbnails. |

---

## 7. File Limits Reference

| Format | Max Size | Max Duration | Max Resolution |
| :--- | :--- | :--- | :--- |
| Image (PNG/JPG) | 5 MB | N/A | 8096 × 8096 px |
| Video (MP4) | 512 MB (Standard) | 2 min 20 sec (140s) | Up to 4K (Premium) |

---

*Sources: X Developer Documentation, Sked Social 2026, Dimensions.com, TweetArchivist, HeyOrca Media Specs*
