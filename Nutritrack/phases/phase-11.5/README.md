# Phase 11.5 - Infrastructure: Build & Deployment Automation

> **Status**: Ready for Implementation  
> **Owner**: Dev Chat  
> **Architect**: Architect Chat  
> **Priority**: **CRITICAL** (Blocks Phase 12)  
> **ETC**: 2-4 days  
> **Structure**: Two parallel workstreams (R22 and R23) + Emergency Recovery  
> **Depends on**: Phase 11 completion  
> **Blocks**: Phase 12 and all subsequent phases  

---

## Overview

This phase addresses **critical infrastructure risks** (R22, R23) identified during the Phase 11 implementation review and test environment creation. The test environment required **4 separate fix commits** to resolve path configuration issues, and multiple incidents of source/compiled file divergence were observed.

**This phase MUST be completed before Phase 12 begins.**

**NEW**: Emergency recovery page added to prevent data loss during deployment failures.

---

## Problem Statement

### R22: Deployment Path Configuration Risk (CRITICAL)
Hardcoded deployment paths in multiple files cause Service Worker scope mismatches and fetch failures.

- **Evidence**: Commits c17724e, a235744, 4bb5961, 1d95e89 in NutriTrack-test repo (2026-08-20)
- **Root Cause**: No centralized path configuration; SW scope must match deployment path exactly; fetch calls must be within SW scope
- **Impact if Unresolved**: Every new environment deployment will fail; high risk of production failure; data loss risk from cache clearing

### R23: Manual Build Process Risk (HIGH)
Manual JSX to JS compilation leads to source/compiled divergence.

- **Evidence**: Multiple commits in NutriTrack repo fixing path/version mismatches between files
- **Root Cause**: No automated build pipeline; manual Babel compilation is error-prone
- **Impact if Unresolved**: Runtime errors; debugging difficulty; wasted developer time

### Emergency Recovery Need
- **Problem**: When deployment breaks app, user cannot export data before clearing cache
- **Solution**: Separate recovery page that works independently of main app

---

## Requirements

### R22 Requirements: Deployment Path Configuration
- Extract ALL deployment paths into single configuration file (deploy-config.js)
- Use relative paths where possible, absolute paths from config where necessary
- Ensure Service Worker scope matches deployment path exactly
- Automated path consistency test (runs on pre-commit)
- Documentation of deployment requirements for each environment

### R23 Requirements: Automated Build Process
- Automated Babel compilation (NutriTrack.jsx to NutriTrack.js)
- Pre-commit hook for build and consistency verification
- Build validation (Babel parse, Node syntax check)
- Documentation of build process

### Emergency Recovery Requirements (NEW)
- Create recover.html emergency recovery page
- Deploy to both production and test environments
- Must work independently of main app (no dependencies on NutriTrack.js)
- Export data from both localStorage and IndexedDB
- Generate downloadable JSON file with timestamp

---

## What

Create a centralized deployment configuration system and automated build process to eliminate manual errors that caused Phase 11 failures. This includes:

1. Centralized path configuration - Single source of truth for all deployment paths
2. Automated JSX to JS compilation - Eliminate source/compiled file divergence
3. Pre-commit validation - Prevent inconsistent commits
4. Emergency recovery page - Data export even when main app is broken

## Why

The Phase 11 deployment failure chain was:
Hardcoded paths -> SW scope mismatch -> App would not load -> Could not export -> Cache clear -> DATA LOST

This phase breaks that chain by:
- Eliminating hardcoded paths (R22)
- Preventing source/compiled divergence (R23)
- Providing recovery even if app breaks (Emergency page)

---

## Implementation Plan

### Step 1: Create Configuration File
Create deploy-config.js at repository root with all path definitions.

### Step 2: Create Build Script
Create build.js for automated Babel compilation.

### Step 3: Create Path Consistency Checker
Create scripts/check-paths.js to validate all paths match config.

### Step 4: Create Pre-commit Hook
Add .husky/pre-commit to run build and path checks before commits.

### Step 5: Create Emergency Recovery Page
Create recover.html - works independently of main app.

### Step 6: Update Existing Files
Update sw.js, index.html, NutriTrack.jsx to use config for all paths.

---

## Files to Create

| File | Description |
|------|-------------|
| deploy-config.js | Centralized path configuration |
| build.js | Automated build script |
| scripts/check-paths.js | Path consistency validator |
| .husky/pre-commit | Pre-commit hook |
| recover.html | Emergency recovery page |

## Files to Modify

| File | Changes |
|------|---------|
| sw.js | Use config for paths |
| index.html | Use config for paths |
| NutriTrack.jsx | Use config for paths |
| package.json | Add Babel dev dependencies |

---

## Code Templates

### deploy-config.js

```javascript
const DEPLOY_CONFIG = {
  BASE_PATH: '/NutriTrack/',
  TEST_BASE_PATH: '/NutriTrack-test/',
  ASSETS: {
    FOODS_JSON: 'foods.json',
    MAIN_JS: 'NutriTrack.js',
    MAIN_JSX: 'NutriTrack.jsx',
    SW_JS: 'sw.js',
    MANIFEST: 'manifest.webmanifest',
    ICONS: 'icons/',
  },
  CACHE_VERSION: 'nutritrack-v74',
  APP_VERSION: '74',
  BABEL_CONFIG: {
    presets: ['@babel/preset-react', '@babel/preset-env'],
    plugins: [],
  },
};

function getPath(assetKey, isTest = false) {
  const base = isTest ? DEPLOY_CONFIG.TEST_BASE_PATH : DEPLOY_CONFIG.BASE_PATH;
  return base + DEPLOY_CONFIG.ASSETS[assetKey];
}

if (typeof module !== 'undefined' && module.exports) {
  module.exports = { DEPLOY_CONFIG, getPath };
}
```

### build.js

```javascript
const fs = require('fs');
const path = require('path');
const babel = require('@babel/core');
const { DEPLOY_CONFIG } = require('./deploy-config.js');

console.log('Building NutriTrack...');

try {
  const jsxPath = path.join(__dirname, DEPLOY_CONFIG.ASSETS.MAIN_JSX);
  const jsPath = path.join(__dirname, DEPLOY_CONFIG.ASSETS.MAIN_JS);
  const jsxCode = fs.readFileSync(jsxPath, 'utf8');
  const result = babel.transformSync(jsxCode, DEPLOY_CONFIG.BABEL_CONFIG);
  fs.writeFileSync(jsPath, result.code);
  console.log('Compiled', jsxPath, '->', jsPath);
  
  let indexHtml = fs.readFileSync('index.html', 'utf8');
  indexHtml = indexHtml.replace(
    /<!-- BUILD_VERSION: .*? -->/,
    '<!-- BUILD_VERSION: ' + DEPLOY_CONFIG.CACHE_VERSION + ' -->'
  );
  fs.writeFileSync('index.html', indexHtml);
  console.log('Updated index.html version');
  console.log('Build successful!');
} catch (error) {
  console.error('Build failed:', error);
  process.exit(1);
}
```

### recover.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NutriTrack - Emergency Recovery</title>
<style>
body { font-family: -apple-system, BlinkMacSystemFont, sans-serif; max-width: 800px; margin: 0 auto; padding: 20px; background: #f5f5f5; }
h1 { color: #d32f2f; }
button { background: #1976d2; color: white; border: none; padding: 12px 24px; border-radius: 4px; font-size: 16px; cursor: pointer; margin: 10px 0; }
button:disabled { background: #cccccc; }
#output { background: white; padding: 15px; border-radius: 4px; margin-top: 20px; white-space: pre-wrap; max-height: 300px; overflow-y: auto; font-family: monospace; font-size: 12px; }
.success { color: #388e3c; }
.error { color: #d32f2f; }
.warning { color: #ff9800; }
</style>
</head>
<body>
<h1>Emergency Recovery</h1>
<p>This page works even if the main app is broken.</p>
<div>
<button onclick="exportData()">Export All Data</button>
<button onclick="checkStorage()">Check Storage</button>
</div>
<h3>Instructions:</h3>
<ol>
<li>Click Export All Data</li>
<li>Save the downloaded JSON file</li>
<li>After fixing the app, use Settings -> Import to restore</li>
</ol>
<h3>Output:</h3>
<pre id="output"></pre>

<script>
const NT_PREFIX = 'nt-';

function log(msg, type) {
  var output = document.getElementById('output');
  var ts = new Date().toLocaleTimeString();
  var cls = type === 'error' ? 'error' : type === 'warning' ? 'warning' : type === 'success' ? 'success' : '';
  output.innerHTML += '<span class="' + cls + '">[' + ts + '] [' + type.toUpperCase() + '] ' + msg + '</span><br>';
  output.scrollTop = output.scrollHeight;
}

function checkStorage() {
  var count = 0, total = 0;
  for (var i = 0; i < localStorage.length; i++) {
    var key = localStorage.key(i);
    if (key.startsWith(NT_PREFIX)) {
      count++;
      var value = localStorage.getItem(key);
      total += key.length + (value ? value.length : 0);
    }
  }
  var pct = (total / (5 * 1024 * 1024) * 100).toFixed(1);
  log('localStorage: ' + count + ' keys, ' + (total/1024/1024).toFixed(2) + ' MB / 5 MB (' + pct + '%)', 'info');
  if (window.indexedDB) checkIndexedDB();
}

function checkIndexedDB() {
  log('Checking IndexedDB...', 'info');
  var request = indexedDB.open('nutritrack', 1);
  request.onerror = function(e) { log('IndexedDB error: ' + e.target.error, 'error'); };
  request.onsuccess = function(e) {
    var db = e.target.result;
    if (!db.objectStoreNames.contains('nutritrack')) { log('No IndexedDB data', 'info'); return; }
    var tx = db.transaction(['nutritrack'], 'readonly');
    var store = tx.objectStore('nutritrack');
    var getAll = store.getAll();
    getAll.onsuccess = function(e) { log('IndexedDB: ' + e.target.result.length + ' records', 'success'); };
    getAll.onerror = function(e) { log('IndexedDB read error: ' + e.target.error, 'error'); };
  };
}

function exportData() {
  var button = event.target;
  button.disabled = true;
  button.textContent = 'Exporting...';
  log('Starting export...', 'info');
  
  var exportData = {
    timestamp: new Date().toISOString(),
    version: '1.0',
    userAgent: navigator.userAgent,
    localStorage: {},
    indexedDB: null
  };
  
  for (var i = 0; i < localStorage.length; i++) {
    var key = localStorage.key(i);
    if (key.startsWith(NT_PREFIX)) {
      try { exportData.localStorage[key] = localStorage.getItem(key); }
      catch (e) { log('Failed to read ' + key + ': ' + e.message, 'error'); }
    }
  }
  log('Exported ' + Object.keys(exportData.localStorage).length + ' localStorage keys', 'success');
  
  if (window.indexedDB) {
    var dbRequest = indexedDB.open('nutritrack', 1);
    dbRequest.onerror = function(e) { log('IndexedDB not available: ' + e.target.error, 'warning'); completeExport(exportData, button); };
    dbRequest.onsuccess = function(e) {
      var db = e.target.result;
      if (db.objectStoreNames.contains('nutritrack')) {
        var tx = db.transaction(['nutritrack'], 'readonly');
        var store = tx.objectStore('nutritrack');
        var getAll = store.getAll();
        getAll.onsuccess = function(e) { exportData.indexedDB = e.target.result; log('Exported ' + exportData.indexedDB.length + ' IndexedDB records', 'success'); completeExport(exportData, button); };
        getAll.onerror = function(e) { log('IndexedDB read failed: ' + e.target.error, 'error'); completeExport(exportData, button); };
      } else { log('No IndexedDB data', 'info'); completeExport(exportData, button); }
    };
  } else { log('IndexedDB not supported', 'warning'); completeExport(exportData, button); }
}

function completeExport(data, button) {
  var now = new Date();
  var dateStr = now.toISOString().split('T')[0];
  var timeStr = now.toISOString().split('T')[1].split('.')[0].replace(/:/g, '-');
  var filename = 'nutritrack-recovery-' + dateStr + '-' + timeStr + '.json';
  var blob = new Blob([JSON.stringify(data, null, 2)], {type: 'application/json'});
  var url = URL.createObjectURL(blob);
  var a = document.createElement('a');
  a.href = url; a.download = filename; a.style.display = 'none';
  document.body.appendChild(a); a.click();
  setTimeout(function() { document.body.removeChild(a); URL.revokeObjectURL(url); }, 100);
  log('EXPORT COMPLETE! File: ' + filename, 'success');
  log('localStorage keys: ' + Object.keys(data.localStorage).length, 'success');
  log('IndexedDB records: ' + (data.indexedDB ? data.indexedDB.length : 0), 'success');
  button.disabled = false; button.textContent = 'Export All Data';
}

log('Recovery tool loaded. Click a button.', 'success');
</script>
</body>
</html>
```

---

## Validation Matrix

| ID | Test | Action | Expected Result |
|----|------|--------|-----------------|
| V1 | Build script | Run node build.js | JS compiled, versions updated |
| V2 | Pre-commit | Modify JSX, commit | Hook runs, checks pass |
| V3 | Path check | Run check-paths.js | No errors |
| V4 | Bad commit | Commit old JS | Rejected |
| V5 | Test deploy | Deploy to test | Works |
| V6 | Prod deploy | Deploy to prod | Works |
| V7 | New env | Create test env | Single commit |
| V8 | Recovery | Break app, open recover.html | Exports data |
| V9 | Cache clear | Clear cache, recover | Still works |
| V10 | Device test | Test on iPhone 16e | All pass |

---

## Acceptance Criteria

### R22: Deployment Path Configuration
- [ ] deploy-config.js created with all path definitions
- [ ] All files use config for paths (no hardcoded paths)
- [ ] Automated test verifies path consistency
- [ ] New test environment with single commit
- [ ] Documentation updated

### R23: Automated Build Process
- [ ] build.js created and tested
- [ ] Pre-commit hook prevents bad commits
- [ ] Build validation working
- [ ] Documentation updated

### Emergency Recovery
- [ ] recover.html created
- [ ] Deployed to both environments
- [ ] Works independently
- [ ] Exports localStorage data
- [ ] Exports IndexedDB data

---

## References

- [R22: Deployment Path Configuration Risk](../../RISK_REGISTER.md#r22-deployment-path-configuration-risk)
- [R23: Manual Build Process Risk](../../RISK_REGISTER.md#r23-manual-build-process-risk)
- [Test Environment Fix Report](../../../Test%20environment/README.md)
- [Development Process](../../DEVELOPMENT_PROCESS.md)
