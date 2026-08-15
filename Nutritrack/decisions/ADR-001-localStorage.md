# ADR-001: Use localStorage for Data Storage

## Status
- Accepted

## Context
NutriTrack needs to store user data on-device for offline functionality.
Considered options: localStorage, IndexedDB, WebSQL, custom file storage.

## Decision
Use localStorage as primary storage with:
- Conservative 5MB cap (practical iOS Safari limit)
- Direct measurement (measureLocalStorageBytes)
- Health indicator (green/yellow/red)
- corruptedKeys guard to prevent overwrite
- IndexedDB as future migration path

## Consequences

### Positive
- Simple API, easy to use
- Synchronous operations
- Works well for current scale
- Well-supported across browsers
- Easy to debug

### Negative
- Limited to ~5MB
- Synchronous operations can block main thread
- No native transaction support
- navigator.storage.estimate() unreliable on iOS

## Alternatives Considered

### IndexedDB
- Pros: Larger storage, asynchronous, transaction support
- Cons: More complex API, overkill for current needs
- Decision: Deferred to future if needed

### Hybrid Approach
- Pros: Use localStorage for small data, IndexedDB for large
- Cons: Complex to implement, data split across stores
- Decision: Not worth complexity

## Migration Path
If storage needs exceed localStorage:
1. Implement IndexedDB wrapper
2. Migrate data from localStorage
3. Update all read/write operations
4. Maintain backward compatibility

Trigger: Sustained usage >4.5MB or performance issues