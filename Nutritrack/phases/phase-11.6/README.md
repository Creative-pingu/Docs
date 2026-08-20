# Phase 11.6 - Data Safety Net: Zero Data Loss Guarantee

> **Status**: Ready for Implementation  
> **Owner**: Dev Chat  
> **Architect**: Architect Chat  
> **Priority**: **CRITICAL** (Prevents future data loss)  
> **ETC**: 2-3 days  
> **Depends on**: Phase 11.5 completion  
> **Blocks**: Phase 12  

---

## Overview

Despite Phase 11.5 preventing deployment breakage, we need a **defense-in-depth** approach to data safety. This phase implements a triple-redundancy storage system to ensure zero data loss, even in catastrophic failure scenarios.

**This phase MUST be completed before Phase 12 begins.**

---

## Problem Statement

Current single-point-of-failure storage approach:
- localStorage has 5MB limit
- If localStorage corrupts, all data is lost
- No recovery mechanism for edge cases
- Phase 11 data loss proved this is a real risk

**Goal**: Zero data loss, even if multiple systems fail.

---

## Requirements

### 1. Dual Storage Layer
- Create nt-storage.js abstraction layer
- All writes go to both localStorage (primary) and IndexedDB (secondary)
- All reads try localStorage first, fallback to IndexedDB
- Automatic fallback if primary fails

### 2. Auto-Export System
- Automatic JSON export after N data changes (default: 5)
- Export stored in memory, downloadable via button
- Export includes all NutriTrack data (logs, goals, custom foods, recipes, settings, etc.)
- Manual export button in Settings panel

### 3. Data Versioning
- Add dataVersion field to stored data
- On load, check version and migrate if needed
- Migration is idempotent and backward compatible
- Migrate water=null values to water=0 (fixing Phase 11 issue)

### 4. Storage Health Monitoring
- Monitor localStorage usage (warn at 70%, error at 90%)
- User notification for storage issues
- Prevent data loss from storage limits

---

## What

Implement a triple-redundancy storage system with:
1. **Dual storage layer** - localStorage (primary) + IndexedDB (secondary)
2. **Auto-export system** - Periodic JSON exports stored in memory
3. **Data versioning** - Safe migration between schema versions
4. **Emergency recovery** - Already implemented in Phase 11.5 (recover.html)

## Why

Defense-in-depth against data loss:
- If localStorage fails → IndexedDB has copy
- If both fail → Auto-export has recent backup
- If migration needed → Versioning ensures safety
- If storage full → Warnings prevent silent data loss

---

## Implementation Plan

### Step 1: Create Storage Abstraction Layer
Create nt-storage.js with:
- NTIndexedDB wrapper class
- NTStorage main class with dual-write
- Auto-export trigger after N changes
- Storage health monitoring

### Step 2: Create Migration Utilities
Create migrations.js with:
- Version detection
- Migration functions (v1 → v2, etc.)
- Water=null → water=0 fix

### Step 3: Update All Data Files
Replace all localStorage calls with ntStorage methods:
- Replace localStorage.setItem/getItem with storage.set/get
- Handle async/await where needed
- Maintain backward compatibility

### Step 4: Add Export UI to Settings
Add manual export button and storage warnings to Settings panel.

---

## Files to Create

| File | Description |
|------|-------------|
| nt-storage.js | Storage abstraction layer with dual-write and auto-export |
| migrations.js | Data migration utilities |

## Files to Modify

| File | Changes |
|------|---------|
| All files using localStorage | Use ntStorage instead |
| Settings component | Add export button and storage warnings |

---

## Code Templates

### nt-storage.js

```javascript
const NT_PREFIX = 'nt-';
const STORAGE_KEYS = [
  'logs', 'goals', 'custom', 'profile', 'settings',
  'recipes', 'supplements', 'exercise', 'state'
];

const STORAGE_CONFIG = {
  AUTO_EXPORT_INTERVAL: 5,
  LOCALSTORAGE_WARN_THRESHOLD: 0.7,
  LOCALSTORAGE_ERROR_THRESHOLD: 0.9,
  DATA_VERSION: 'v2',
  INDEXEDDB_NAME: 'nutritrack',
  INDEXEDDB_STORE: 'nutritrack'
};

class NTIndexedDB {
  constructor() {
    this.dbPromise = this._openDB();
    this.ready = false;
    this.db = null;
    this.dbPromise.then(db => { this.ready = true; this.db = db; })
      .catch(err => { console.warn('IndexedDB not available:', err); this.ready = true; });
  }
  
  async _openDB() {
    return new Promise((resolve, reject) => {
      const request = indexedDB.open(STORAGE_CONFIG.INDEXEDDB_NAME, 2);
      request.onerror = () => reject(request.error);
      request.onsuccess = () => resolve(request.result);
      request.onupgradeneeded = (e) => {
        const db = e.target.result;
        if (!db.objectStoreNames.contains(STORAGE_CONFIG.INDEXEDDB_STORE)) {
          db.createObjectStore(STORAGE_CONFIG.INDEXEDDB_STORE, { keyPath: 'key' });
        }
      };
    });
  }
  
  async get(key) {
    if (!this.ready) await this.dbPromise.catch(() => {});
    if (!this.db) return undefined;
    return new Promise(resolve => {
      try {
        const tx = this.db.transaction([STORAGE_CONFIG.INDEXEDDB_STORE], 'readonly');
        const store = tx.objectStore(STORAGE_CONFIG.INDEXEDDB_STORE);
        const request = store.get(key);
        request.onsuccess = () => resolve(request.result?.value);
        request.onerror = () => resolve(undefined);
      } catch (e) { resolve(undefined); }
    });
  }
  
  async set(key, value) {
    if (!this.ready) await this.dbPromise.catch(() => {});
    if (!this.db) return;
    return new Promise(resolve => {
      try {
        const tx = this.db.transaction([STORAGE_CONFIG.INDEXEDDB_STORE], 'readwrite');
        const store = tx.objectStore(STORAGE_CONFIG.INDEXEDDB_STORE);
        const request = store.put({ key, value, timestamp: Date.now() });
        request.onsuccess = () => resolve();
        request.onerror = () => resolve();
      } catch (e) { resolve(); }
    });
  }
}

class NTStorage {
  constructor() {
    this.indexedDB = new NTIndexedDB();
    this.changeCount = 0;
    this.exportQueue = [];
    this._loadChangeCount();
    this._migrateIfNeeded();
  }
  
  _loadChangeCount() {
    try { this.changeCount = parseInt(localStorage.getItem('nt-change-count') || '0'); }
    catch (e) { this.changeCount = 0; }
  }
  
  _saveChangeCount() {
    try { localStorage.setItem('nt-change-count', this.changeCount.toString()); }
    catch (e) {}
  }
  
  async _migrateIfNeeded() {
    try {
      const currentVersion = localStorage.getItem('nt-data-version');
      if (currentVersion !== STORAGE_CONFIG.DATA_VERSION) {
        localStorage.setItem('nt-data-version', STORAGE_CONFIG.DATA_VERSION);
      }
    } catch (e) { console.error('Migration check failed:', e); }
  }
  
  async get(key) {
    const storageKey = NT_PREFIX + key;
    try {
      const value = localStorage.getItem(storageKey);
      if (value !== null) return JSON.parse(value);
    } catch (e) { console.warn('localStorage read failed, trying IndexedDB:', e); }
    
    try {
      const value = await this.indexedDB.get(key);
      if (value !== undefined) {
        this._writeLocalStorage(storageKey, value);
        return value;
      }
    } catch (e) { console.warn('IndexedDB read failed:', e); }
    return null;
  }
  
  async set(key, value) {
    const storageKey = NT_PREFIX + key;
    this._writeLocalStorage(storageKey, value);
    await this._writeIndexedDB(key, value);
    this._trackChange(key, value);
  }
  
  _writeLocalStorage(key, value) {
    try { localStorage.setItem(key, JSON.stringify(value)); return true; }
    catch (e) { console.error('localStorage write failed:', e); return false; }
  }
  
  async _writeIndexedDB(key, value) {
    try { await this.indexedDB.set(key, value); } catch (e) {}
  }
  
  _trackChange(key, value) {
    this.changeCount++;
    this._saveChangeCount();
    this.exportQueue.push({ key, value, timestamp: Date.now() });
    if (this.changeCount % STORAGE_CONFIG.AUTO_EXPORT_INTERVAL === 0) {
      this.triggerAutoExport();
    }
  }
  
  triggerAutoExport() {
    if (this.exportQueue.length === 0) return;
    window.ntAutoExport = this._collectExportData();
    this.exportQueue = [];
  }
  
  _collectExportData() {
    const exportData = { timestamp: new Date().toISOString(), version: STORAGE_CONFIG.DATA_VERSION, data: {} };
    STORAGE_KEYS.forEach(key => {
      try {
        const value = localStorage.getItem(NT_PREFIX + key);
        if (value !== null) exportData.data[key] = JSON.parse(value);
      } catch (e) {}
    });
    return exportData;
  }
  
  getAutoExport() { return window.ntAutoExport; }
  
  checkStorageHealth() {
    const warnings = [];
    const usage = this._calculateLocalStorageUsage();
    if (usage > STORAGE_CONFIG.LOCALSTORAGE_WARN_THRESHOLD) {
      warnings.push('localStorage ' + Math.round(usage * 100) + '% full');
    }
    if (usage > STORAGE_CONFIG.LOCALSTORAGE_ERROR_THRESHOLD) {
      warnings.push('localStorage nearly full - consider cleaning old data');
    }
    return warnings;
  }
  
  _calculateLocalStorageUsage() {
    let total = 0;
    for (let i = 0; i < localStorage.length; i++) {
      const key = localStorage.key(i);
      if (key.startsWith(NT_PREFIX)) {
        const value = localStorage.getItem(key);
        total += key.length + (value ? value.length : 0);
      }
    }
    return total / (5 * 1024 * 1024);
  }
}

const storage = new NTStorage();
window.ntStorage = storage;

export default storage;
```

### migrations.js

```javascript
export function runMigrations() {
  try {
    const currentVersion = localStorage.getItem('nt-data-version') || 'v1';
    if (currentVersion === 'v1') {
      migrateV1ToV2();
      localStorage.setItem('nt-data-version', 'v2');
    }
  } catch (e) { console.error('Migration failed:', e); }
}

function migrateV1ToV2() {
  try {
    const custom = localStorage.getItem('nt-custom');
    if (custom) {
      const customData = JSON.parse(custom);
      let modified = false;
      for (const [id, food] of Object.entries(customData)) {
        if (food.water === null) { food.water = 0; modified = true; }
      }
      if (modified) localStorage.setItem('nt-custom', JSON.stringify(customData));
    }
  } catch (e) { console.error('Custom foods migration failed:', e); }
}
```

### Settings Panel Integration

```javascript
function DataExportSettings() {
  const [lastExport, setLastExport] = useState(null);
  const [storageWarnings, setStorageWarnings] = useState([]);
  
  useEffect(() => {
    if (window.ntAutoExport) setLastExport(new Date(window.ntAutoExport.timestamp));
    if (window.ntStorage) setStorageWarnings(window.ntStorage.checkStorageHealth());
  }, []);
  
  const handleManualExport = () => {
    if (window.ntStorage) {
      window.ntStorage.triggerAutoExport();
      const exportData = window.ntStorage.getAutoExport();
      if (exportData) {
        const blob = new Blob([JSON.stringify(exportData, null, 2)], { type: 'application/json' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'nutritrack-export-' + new Date().toISOString().split('T')[0] + '.json';
        a.click();
        URL.revokeObjectURL(url);
      }
    }
  };
  
  return (
    <div className="settings-section">
      <h3>Data Export</h3>
      {storageWarnings.length > 0 && (
        <div className="warning-box">
          <strong>Storage Warnings:</strong>
          <ul>{storageWarnings.map((w, i) => <li key={i}>{w}</li>)}</ul>
        </div>
      )}
      <p>Last auto-export: {lastExport ? lastExport.toLocaleString() : 'Never'}</p>
      <p>Auto-exports every {STORAGE_CONFIG.AUTO_EXPORT_INTERVAL} data changes.</p>
      <button onClick={handleManualExport}>Manual Export Now</button>
    </div>
  );
}
```

---

## Validation Matrix

| ID | Test | Action | Expected Result |
|----|------|--------|-----------------|
| V1 | Dual write | Write data | Stored in both localStorage and IndexedDB |
| V2 | Primary read | Read data | Retrieved from localStorage |
| V3 | Fallback read | Corrupt localStorage, read | Retrieved from IndexedDB |
| V4 | Auto-export | Make 5 changes | Auto-export triggered |
| V5 | Manual export | Click export button | JSON file downloaded |
| V6 | Storage warning | Fill to 70% | Warning displayed |
| V7 | Storage error | Fill to 90% | Error displayed |
| V8 | Version migration | Load old data | Migration runs automatically |
| V9 | Water migration | Load v1 data | water=null converted to 0 |
| V10 | Backward compat | Load old app | Works without errors |
| V11 | Offline export | Export offline | Works, file downloads |
| V12 | Device validation | Test on iPhone 16e | All tests pass |

---

## Acceptance Criteria

- [ ] nt-storage.js created and all data flows through it
- [ ] Dual-write to localStorage + IndexedDB working
- [ ] Fallback read from IndexedDB when localStorage fails
- [ ] Auto-export triggers after N changes
- [ ] Manual export button works
- [ ] Data versioning and migration working
- [ ] Water=null values migrated to 0
- [ ] Storage health warnings displayed
- [ ] All validation matrix tests pass
- [ ] Nick confirms on iPhone 16e

---

## Technical Notes

- No new runtime dependencies
- IndexedDB built into all modern browsers
- Must maintain backward compatibility
- Must work offline
- Must not impact performance
- Must pass device validation on iPhone 16e

**Storage Capacity:**
- localStorage: 5MB (iOS Safari limit)
- IndexedDB: ~50MB+ (varies by browser)
- Combined: ~55MB+ available

---

## References

- [Phase 11.5: Infrastructure](phase-11.5/README.md) (must be completed first)
- [RISK_REGISTER](../../RISK_REGISTER.md)
- [DEVELOPMENT_PROCESS](../../DEVELOPMENT_PROCESS.md)
