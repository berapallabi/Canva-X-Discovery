# Field Validations & System Limitations

This document serves as the absolute source of truth for all quantitative constraints within the Canva X Publisher integration. The UI MUST enforce these validations *before* attempting API transmission to ensure a frictionless user experience.

## 1. Media Constraints (X Native API)

| Media Type | Max File Size | Constraints | UI Validation Rule |
| :--- | :--- | :--- | :--- |
| **Static Images** (JPG, PNG, WEBP) | `5 MB` per image | Max dimensions: 8192x8192px.<br>Max quantity: 4 per tweet. | **Block & Disable:** If user selects >4 images, disable selection checkboxes. If an export exceeds 5MB, display inline error: *"Image exceeds 5MB limit. Please compress design."* |
| **Animated GIFs** | `15 MB` | Max quantity: 1 per tweet.<br>Cannot mix GIF with images/videos. | **Block:** If GIF >15MB, show error. If user attempts to select a GIF + an Image, display conflict error. |
| **Video** (MP4, MOV) | `512 MB` | Max duration: `140 seconds` (2m20s).<br>Max resolution: 1920x1200 or 1200x1920.<br>Max framerate: 60fps.<br>Max quantity: 1 per tweet.<br>Cannot mix with images. | **Block:** If export >140s, show error: *"Video must be under 2m20s. Please trim your design."* Prevent mixing with images. The backend proxy MUST chunk uploads at `3.9MB`. |

## 2. Text & Metadata Constraints (X Native API)

| Input Field | Maximum Limit | Overflow Behavior (UI Rule) |
| :--- | :--- | :--- |
| **Caption Text** | `280 characters` (Standard)<br>`25,000 characters` (Premium) | **Warn & Block:** Show a live decrementing counter. **[API LIMIT]:** X does not broadcast Premium status. UI must manually ask user if they are Premium to unlock 25,000 limit. |
| **Alt-Text** | `1000 characters` per media item | **Block:** Display character limit on Alt-Text input field. Prevent typing >1000 chars. |
| **User Tagging (@)** | `10 users` per visual media item | **[BLOCKED BY X API v2]:** Native image tagging unsupported. Warn user that tags will be converted into standard `@mentions` appended to the text caption. |
| **Sensitive Flag** | Boolean | **[BLOCKED BY X API v2]:** X API v2 dropped per-tweet support. UI checkbox acts as a placebo for the Canva Preview only. |
| **Thread Depth** | `25 tweets` maximum per thread | **Block:** If the Canva flow attempts to chain >25 pages into a sequential thread, block the thread toggle and show error. |

## 3. Infrastructural Constraints

| System | Limitation | UI / Backend Enforcement |
| :--- | :--- | :--- |
| **Canva SDK** | `5 KB` maximum payload size for the `PublishRef` object. | **Silent Truncation / Block:** If the serialized array of URLs + Captions exceeds 5KB, Canva API will crash. The UI must measure byte size of metadata before calling `publishContent()`. |
| **Vercel Serverless** | `4.5 MB` payload limit per POST request. | **Frontend Chunking:** The Next.js proxy crashes on >4.5MB requests. The Canva React app MUST slice MP4 binaries into `3.9MB` chunks before executing `fetch()` to the proxy. |
| **Vercel Timeout** | `10s` (Free) / `60s` (Pro) function timeout. | **Iterative Fetching:** A monolithic upload of a 512MB video will timeout. The frontend MUST orchestrate the upload loop locally, calling the proxy for each chunk individually to reset the timeout clock. |
| **X API Rate Limits** | `50 tweets/24h` / `300 tweets/3h` (Varies by tier). | **Intercept & Block:** The UI must parse the `x-rate-limit-reset` header. If a `429` is returned, the UI renders: *"Rate Limit Exceeded. You can publish again on [Date/Time]."* |
