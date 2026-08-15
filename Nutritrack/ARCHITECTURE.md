# NutriTrack — Architecture Documentation

> **Last Updated**: 2026-08-14
> **Owner**: Architect Chat
> **Status**: Current

---

## Overview

Progressive Web App (PWA) with local-first data storage and offline capabilities.

## System Architecture

```
iPhone 16e (Safari)
├── NutriTrack PWA (React)
│   ├── Single JSX file
│   ├── React 18 (CDN)
│   └── Babel (CDN)
├── Service Worker (sw.js)
│   ├── Precache assets
│   ├── Fetch handler
│   └── Update banner
└── localStorage
    ├── nt-logs
    ├── nt-goals
    ├── nt-custom
    └── ... (other keys)
```

## Key Components

### Frontend
- React PWA, single JSX file
- Dependencies: React, Babel, Lucide, JSZip (all from CDN)
- External foods.json (~342KB, 830 records)

### Service Worker
- Precaches same-origin assets only
- CDN assets excluded (cached naturally by browser)
- index.html excluded from precache and fetch handler
- Current CACHE_VERSION: nutritrack-v42 (after 6m-4)

### Cloudflare Worker
- URL: nutritrack-proxy.nickkropf.workers.dev
- Purpose: Notion API proxy
- Features: Origin allowlist, encrypted credentials, read-only
- Health endpoint: /health (for offline detection)

### Data Storage
- localStorage with 5MB cap
- measureLocalStorageBytes() for direct measurement
- Health indicator: Green (<70%), Yellow (70-90%), Red (>90%)
- lastValidatedAt timestamp on every successful load
- corruptedKeys guard prevents overwrite of failed-to-parse keys