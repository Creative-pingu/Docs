# Test Environment Fix Report: Food Database Load Error

## Overview

On 2026-08-20, a critical issue was reported where the NutriTrack test environment (`NutriTrack-test` repo) displayed the error message: **"The food database could not be loaded. Please reload the app."** when users attempted to add foods.

This report documents the root cause analysis, the series of fixes applied, and the lessons learned during the debugging process.

---

## Timeline

| Time | Event |
|------|-------|
| ~2026-08-17 | Test repo initially set up with root-based paths (no `/NutriTrack/` prefix) |
| 2026-08-19 | Commit `e47bf80` changed paths from `/NutriTrack/` to root in index.html, manifest, sw.js |
| 2026-08-19 | Commit `5bc60ba` fixed NutriTrack.js to use global React and set `window._MainApp` |
| 2026-08-19 | Commit `cbf56cf` ensured both `window.NutriTrack` and `window._MainApp` are set |
| 2026-08-20 | User reported food database load error on test |
| 2026-08-20 | First fix: `c17724e` - Updated `NutriTrack.jsx` fetch path from `/NutriTrack/foods.json` to `/foods.json` |
| 2026-08-20 | User reported issue persisted |
| 2026-08-20 | Second fix: `a235744` - Bumped versions to clear stale SW cache (v73 → v74-test) |
| 2026-08-20 | User reported issue persisted |
| 2026-08-20 | Third fix: `4bb5961` - Fixed SW scope and all paths to `/NutriTrack-test/` prefix |
| 2026-08-20 | User reported issue persisted |
| 2026-08-20 | **Fourth fix: `1d95e89` - Fixed fetch path to `/NutriTrack-test/foods.json`** |
| 2026-08-20 | Issue resolved on test repo |

---

## Root Causes

### 1. Path Mismatch in Source Code

**Issue**: The `loadFoodDB()` function in `NutriTrack.jsx` was hardcoded to fetch from `/NutriTrack/foods.json`, but the test repository uses root-based paths (no `/NutriTrack/` prefix).

**Commit**: `e47bf80` had changed index.html, manifest.webmanifest, and sw.js to use root paths, but `NutriTrack.jsx` was not updated.

**Fix**: Commit `c17724e` updated the fetch path in `NutriTrack.jsx` from `/NutriTrack/foods.json` to `/foods.json`.

### 2. Version Mismatch Between Shell and Service Worker

**Issue**: `index.html` had `SHELL_APP_VERSION = 'v73'` but `sw.js` had `CACHE_VERSION = "nutritrack-v61-test"`. Per the tooling notes, these versions must be bumped together.

**Impact**: The old Service Worker (v61) remained active, serving stale cached `NutriTrack.js` that had incorrect paths.

**Fix**: Commit `a235744` bumped both versions to `v74-test` / `nutritrack-v74-test`.

### 3. Service Worker Scope Mismatch

**Issue**: The Service Worker was registered with scope `/` (root) but the test repository is deployed at `/NutriTrack-test/`. The SW was looking for files at root paths (`/foods.json`, `/NutriTrack.js`) instead of `/NutriTrack-test/foods.json` and `/NutriTrack-test/NutriTrack.js`.

**Impact**: The SW precached assets at the wrong locations and fetch handlers matched the wrong paths.

**Fix**: Commit `4bb5961` updated:
- SW registration: `/sw.js` → `/NutriTrack-test/sw.js`, scope `/` → `/NutriTrack-test/`
- SW `PRECACHE_ASSETS`: All paths prefixed with `/NutriTrack-test/`
- SW fetch handlers: Updated pathname checks to `/NutriTrack-test/`
- index.html manifest and icon paths: Prefixed with `/NutriTrack-test/`

### 4. Fetch Path Outside Service Worker Scope

**Issue**: After fixing the SW scope to `/NutriTrack-test/`, the fetch call was still using `/foods.json` (without prefix). Since `/foods.json` is **outside** the SW's scope, the Service Worker never intercepted the request. The browser tried to fetch `/foods.json` directly, which returned 404.

**Impact**: This was the final blocker. The SW was correctly configured for `/NutriTrack-test/`, but the app was fetching from `/foods.json`.

**Fix**: Commit `1d95e89` changed the fetch path from `/foods.json` to `/NutriTrack-test/foods.json` in both `NutriTrack.jsx` and `NutriTrack.js`.

---

## Complete Fix Summary

| Commit | Repository | Files Changed | Description |
|--------|------------|---------------|-------------|
| `c17724e` | NutriTrack-test | `NutriTrack.jsx` | Fixed fetch path: `/NutriTrack/foods.json` → `/foods.json` |
| `a235744` | NutriTrack-test | `index.html`, `sw.js` | Bumped versions: v73/v61-test → v74-test |
| `4bb5961` | NutriTrack-test | `index.html`, `sw.js` | Fixed SW scope and paths to `/NutriTrack-test/` |
| `1d95e89` | NutriTrack-test | `NutriTrack.jsx`, `NutriTrack.js` | Fixed fetch path: `/foods.json` → `/NutriTrack-test/foods.json` |
| `11890db` | NutriTrack | `index.html`, `NutriTrack.js` | Fixed version mismatch (v73 → v74) and JS path mismatch |

---

## Lessons Learned

### 1. Version Bumping Must Be Atomic

**Lesson**: When bumping app versions, `SHELL_APP_VERSION` in `index.html` and `CACHE_VERSION` in `sw.js` **must** be changed together. A mismatch causes the old Service Worker to remain active, serving stale cached assets.

**Tooling Note**: The project documentation explicitly states: *"CACHE_VERSION in sw.js and APP_VERSION in index.html must be bumped together if either changes, and the version-comment in index.html must match."*

**Recommendation**: Create a build script that automates version bumping across all files to prevent human error.

### 2. Service Worker Scope Must Match Deployment Path

**Lesson**: The Service Worker scope must match the actual deployment path of the application.

- Main repo deployed at: `/NutriTrack/` → SW scope: `/NutriTrack/`
- Test repo deployed at: `/NutriTrack-test/` → SW scope: `/NutriTrack-test/`

**Impact**: A mismatched scope means the SW won't intercept requests for assets within the app, and precached assets won't be found.

**Recommendation**: Document the deployment path for each repository and ensure all path references (SW registration, fetch URLs, precache assets) are consistent.

### 3. Fetch Paths Must Be Within SW Scope

**Lesson**: Any fetch requests that should be intercepted by the Service Worker must use paths within the SW's scope.

**Example**: If SW scope is `/NutriTrack-test/`, then `fetch('/foods.json')` will **not** be intercepted (it's outside the scope). Use `fetch('/NutriTrack-test/foods.json')` instead.

**Recommendation**: Use relative paths (e.g., `./foods.json`) or ensure absolute paths include the deployment prefix.

### 4. Compiled JS Must Match Source JS

**Lesson**: `NutriTrack.js` is a compiled version of `NutriTrack.jsx`. If the source file is updated, the compiled file must also be updated (or recompiled).

**Impact**: In the main repo, `NutriTrack.jsx` had `/NutriTrack/foods.json` but `NutriTrack.js` had `/foods.json`. This inconsistency caused the fetch to fail.

**Recommendation**: 
- Either maintain `NutriTrack.jsx` as the source of truth and recompile `NutriTrack.js` on every change
- Or update both files simultaneously when making path changes
- Consider adding a pre-commit hook to verify path consistency between source and compiled files

### 5. GitHub Pages Deployment Paths

**Lesson**: GitHub Pages serves repository content at `https://<user>.github.io/<repo>/`. For the `NutriTrack-test` repo, this means:
- Files in the repo root → served at `/NutriTrack-test/`
- The app must use paths relative to `/NutriTrack-test/`, not `/`

**Common Mistake**: Assuming the test repo is deployed at root (`/`) when it's actually at `/NutriTrack-test/`.

**Recommendation**: Clearly document the deployment URL for each repository and test all paths against the actual deployment.

### 6. Service Worker Caching Can Mask Issues

**Lesson**: A stale Service Worker can serve old cached assets even after new files are deployed. This can make it appear that a fix didn't work.

**Debugging Tips**:
1. Open DevTools → Application → Service Workers
2. Click "Unregister" to remove the old SW
3. Hard refresh (Ctrl+Shift+R or Cmd+Shift+R) to bypass cache
4. Check the "Update" button to force SW update

**Recommendation**: Include SW version in the error logging to help diagnose caching issues.

### 7. Test on Actual Deployment, Not Just Local

**Lesson**: Path issues may not be visible when testing locally (where files are served from `/`). They only manifest when deployed to GitHub Pages with the actual path structure.

**Recommendation**: Always test changes on the actual GitHub Pages deployment, especially for path-related changes.

---

## Debugging Checklist

For future path-related issues, use this checklist:

1. **Verify deployment path**
   - Where is the repo deployed? (`/` or `/NutriTrack-test/` or `/NutriTrack/`)?
   
2. **Check Service Worker registration**
   - Does the SW URL match the deployment path?
   - Does the scope match the deployment path?
   
3. **Check fetch paths**
   - Are all fetch calls using paths within the SW scope?
   - Do the paths match the actual file locations?
   
4. **Check precache assets**
   - Do `PRECACHE_ASSETS` paths match the deployment path?
   - Do they match the paths used in fetch calls?
   
5. **Check version consistency**
   - Does `SHELL_APP_VERSION` match `CACHE_VERSION`?
   - Does the build-info comment match?
   
6. **Check source vs compiled consistency**
   - Do `NutriTrack.jsx` and `NutriTrack.js` have matching paths?
   
7. **Clear browser cache**
   - Unregister old Service Worker
   - Hard refresh the page
   - Clear site data if needed

---

## Files Modified

### NutriTrack-test Repository
- `NutriTrack.jsx` - Fixed fetch path to `/NutriTrack-test/foods.json`
- `NutriTrack.js` - Fixed fetch path to `/NutriTrack-test/foods.json`
- `index.html` - Fixed SW registration, manifest path, icon path, version
- `sw.js` - Fixed SW scope, precache assets, fetch handlers, version

### NutriTrack Repository
- `NutriTrack.js` - Fixed fetch path to `/NutriTrack/foods.json`, fixed version
- `index.html` - Fixed version from v73 to v74

---

## Verification

After all fixes were applied:
- Test repo build: #1164024676 - **built** ✅
- Main repo build: #1163644338 - **built** ✅
- Food database loads successfully on both deployments ✅

---

## Prevention

To prevent similar issues in the future:

1. **Automated build process**: Create a build script that:
   - Compiles `NutriTrack.jsx` → `NutriTrack.js`
   - Validates path consistency between source and compiled files
   - Bumps all version numbers atomically
   
2. **Path configuration**: Extract all deployment paths into a configuration file that's used by both the app and the SW.

3. **Integration tests**: Add automated tests that verify:
   - All fetch paths are within SW scope
   - All precache assets exist and match fetch paths
   - Version numbers are consistent across files
   
4. **Documentation**: Update the project documentation to clearly state:
   - Deployment path for each repository
   - Version bumping requirements
   - SW scope requirements
