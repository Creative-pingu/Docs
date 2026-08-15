# Phase 6m — Service Worker Update Banner Fix

> **Status**: In Progress
> **Owner**: Dev Chat
> **Next Action**: Awaiting 6m-4 device validation

---

## Overview
The update banner had never triggered without a full cache/history clear.
This phase identifies and fixes the root causes sequentially.

## Iterations

### 6m-1: index.html in Precache
**Root Cause**: index.html being served from cache suppressed SW re-registration.
**Fix**: Removed index.html from PRECACHE_ASSETS and fetch handler.
**Status**: Deployed

### 6m-2: Diagnostic Only
**Action**: Deployed ?swdebug=1 overlay.
**Finding**: Confirmed updatefound event was not firing.
**Status**: Completed (diagnostic only)

### 6m-3: HTTP Cache Stale sw.js
**Root Cause**: GitHub Pages HTTP cache returning stale sw.js to reg.update().
**Fix**: Added cache: no-store fetch before reg.update() in index.html.
**Result**: updatefound now fires correctly.
**Status**: Deployed

### 6m-4: CDN Redirect in cache.addAll()
**Root Cause**: cache.addAll() is all-or-nothing. lucide@0.344.0 has no file extension and unpkg redirects to a resolved path. The redirect response causes cache.addAll() to reject, the install events waitUntil rejects, and the SW is silently discarded.
**Fix**:
1. Remove all four unpkg CDN URLs from PRECACHE_ASSETS
2. Add missing foods.json (was not in precache)
3. Bump CACHE_VERSION from nutritrack-v41 to nutritrack-v42
**Rationale**: CDN assets will be cached naturally by the browser after first online visit.
**Status**: Brief sent to dev chat — awaiting device validation

## Current State
| Component | State |
|-----------|-------|
| sw.js | CACHE_VERSION = nutritrack-v41 (v42 pending deploy) |
| index.html | Has no-store fetch fix |
| Debug instrumentation | Removed after 6m-3 |
| Production | Waiting for 6m-4 deploy |

## Validation Required for 6m-4
1. Deploy to main, wait for Pages build.
2. Hard-reload in Safari — do NOT clear website data.
3. Confirm the update banner appears and the new SW activates.
4. Tap OK, confirm reload, confirm new JSX is visible.
5. This is the first full end-to-end banner flow exercise in production — validate all four steps explicitly.
6. If banner does not appear: re-enable ?swdebug=1 and report full log to architect before any further changes.

## References
- [Phase 6m-4 Brief](../../../uploads/Phase%206m-4%20Brief.md)
- [Current Status](../../../uploads/Current%20Status%202026-05-28.md)