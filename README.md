# predictive-prefetch
Smart predictive prefetch script that fetches only Cloudflare-cached pages after user intent is detected.
Reduces bandwidth, improves perceived speed, and avoids wasteful preloading.
Includes cache-control and CF-Cache-Status filtering.
Built for performance-focused websites.

This script ensures that only already-cached internal links are prefetched after lightweight, passive user engagement (e.g., hover or touch).
It reduces bandwidth, avoids unnecessary network usage, and improves perceived speed.
---

## 🚀 Why Use This?

Traditional prefetching can waste bandwidth by fetching uncached pages.  
**This script avoids that** — it only prefetches pages that are already cached (`HIT`) by Cloudflare and not excluded via `Cache-Control` headers (`no-cache`, `no-store`, `max-age=0`).

---

## ⚙️ Key Features

1. **User Intent-Based Prefetching**
   - Triggered on:
     - `mouseover` (desktop)
     - `touchstart` (mobile)
     - `mousedown` (if enabled)
     - `viewport` (if configured)

2. **Prefetch Only Cached Content**
   - Sends a `HEAD` request first
   - Checks:
     - `CF-Cache-Status: HIT`
     - `Cache-Control` and `Expires` headers
   - Only then issues a `GET` to trigger prefetching
   - Prevents wasted network activity.
   - If already cached in browser or service worker: skips network fetch and no origin load

3. **Prefetch Throttling**
   - First prefetch: 500ms delay
   - Second: 1000ms
   - Third: 2000ms
   - Limits to 3 URLs per session (`pm = 3`)
   • Prevents over-fetching, balances speed and bandwidth

4. **De-duplication**
   - Uses a `Set` to track prefetched URLs
   - No repeat fetches in same session, on reload reset itself

5. **Blacklist & Filters**
   - Avoids specified paths like `/folder1/`, `/folder2/`, `/url` etc.
   - Skips:
     - Excludes certain URL paths from prefetching
     - External domains (unless allowed)
     - Same-page `#anchors`
     - Links with `data-noInstant`

6. **Data Attribute Support**
   - `<a data-instant>`: Force-enable prefetch
   - `<a data-noInstant>`: Exclude from prefetch
   - `<body data-instant-intensity="viewport">`: Enable on scroll into view
   - `<body data-instant-allow-external-links>`: Allow external origins
   - `<body data-instant-allow-query-string>`: Allow query strings

---

## ✨ What's Unique in This Fork?

This is a **heavily modified fork** of [https://instant.page/5.2.0](https://instant.page/license) with the following **enhancements**:

| Feature                                                              | Source        |
|----------------------------------------------------------------------|---------------|
| ✅ Uses `HEAD` first to validate cache status                         | **Added** by us |
| ✅ Skips `GET` if `CF-Cache-Status` not `HIT`                         | **Added** by us |
| ✅ Skips uncacheable content (`no-cache`, `no-store`, `max-age=0`)   | **Added** by us |
| ✅ Fully avoids `<link rel="prefetch">` race conditions               | **Modified**   |
| ✅ Adaptive throttling between prefetches                             | **Modified**   |
| ✅ More precise blacklist routing                                     | **Added** by us |
| ✅ Works safely with Cloudflare + JS fetch                            | **Added** by us |

---

## 💡 Example Usage

```html
<body
  data-instant-intensity="viewport"
  data-instant-allow-query-string
  data-instant-allow-external-links
>
  <a href="/about">About Us</a>
  <a href="/tools" data-noInstant>Do not prefetch</a>

  <script src="predictive-prefetch.min.js" defer></script>
</body>
```


📄 License
Original engine: © 2019–2024 Alexandre Dieulot – MIT License
instant.page license [https://instant.page/license]

This modified version: © 2025 Affluent Web Media – Licensed under Apache License 2.0

🌐 Credits & SEO
Maintained by [Affluent Web Media](https://affluentwebmedia.com/)
Performance-focused script designed for high-speed websites and technical SEO use cases.

Tested in simplified, GET-based form on:
• [AstrologyFutureEye.com](https://astrologyfutureeye.com/)
• [AstroLookup.com](https://www.astrolookup.com/)

🧠 Notes
This script does not use <link rel="prefetch"> to avoid prefetch race conditions or wasted requests.

Cache is validated before fetching full response.

No Service Worker required, but works well with SW-enabled environments.
