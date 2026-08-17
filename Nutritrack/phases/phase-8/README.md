# Phase 8 - Platform Reliability

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Combines**: A1, A2, R7, R8
> **Depends on**: Phase 7 completion
> **ETC**: 3-5 hours

---

## Problem Statement
Current offline detection uses Worker /health endpoint with 4000ms timeout. `navigator.onLine` is unreliable on iOS standalone mode. Error messages are technical and not user-friendly. These issues affect user experience and reliability.

## Requirements

### Offline Detection (A1 / R7)
- [ ] Improve offline/online state detection reliability
- [ ] Implement hybrid approach: navigator.onLine + Worker fallback
- [ ] Add user-explicit offline mode toggle in Settings
- [ ] Implement local caching of last known state
- [ ] Optimize timeout from 4000ms to 1000ms for Worker health check

### Error Handling UX (A2 / R8)
- [ ] Replace technical error messages with user-friendly ones
- [ ] Provide clear recovery instructions for each error type
- [ ] Separate debug build from production build
- [ ] Add local-only error logging for post-mortem analysis

## Current Implementation Issues
- Worker /health endpoint: 4000ms timeout may be too long for UX
- navigator.onLine: Unreliable on iOS PWA standalone mode
- Error messages: Too technical for end users (e.g., "network: fetch failed", "worker_502: notion_unreachable")
- No user control over offline mode

## Proposed Solution

### Detection Strategy
```
1. Check navigator.onLine (fast, but unreliable on iOS)
2. If online: probe Worker /health with 1000ms timeout
3. If timeout/fail: check local cache state
4. If still uncertain: use last known state
5. Provide user toggle to force offline mode (stored in localStorage)
```

### Error Message Mapping
| Current Technical Message | New User-Friendly Message | Recovery Path |
|----------------------------|---------------------------|--------------|
| network: fetch failed | No internet connection. Some features limited. | Check connection, retry |
| worker_502: notion_unreachable | Recipe sync unavailable. Try again later. | Wait, retry later |
| foods.json fetch failed: 404 | Food database missing. Please reload app. | Reload page |
| SW registration failed | App update failed. Continue offline. | None needed |
| Storage error | Data save failed. Please retry. | Retry action |

### User Toggle
- Add switch in Settings: "Offline Mode"
- localStorage key: `nt-offline-mode`
- When ON: app behaves as if offline regardless of actual connectivity
- When OFF: app uses detection strategy above

## Acceptance Criteria
- [ ] Offline state detected accurately within 2 seconds
- [ ] User can manually toggle offline mode in Settings
- [ ] All error messages are user-friendly and actionable
- [ ] Recovery paths are clear for each error type
- [ ] No data loss during connectivity transitions
- [ ] Debug mode separate from production (optional)
- [ ] Error logs stored locally for diagnostics

## Technical Notes
- No new runtime dependencies
- Offline toggle: localStorage key `nt-offline-mode` (boolean)
- Error messages: Centralized error handler component
- Timeout: Reduce from 4000ms to 1000ms for Worker health check
- Error logging: localStorage key `nt-error-logs` (array of objects)
- Feature detection: Use `navigator.onLine` with fallback

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Disable WiFi in iOS Settings | App detects offline within 2s |
| V2 | Enable airplane mode | Offline state updates correctly |
| V3 | Toggle offline mode in Settings | App respects manual override |
| V4 | Re-enable connectivity | App recovers gracefully |
| V5 | Worker down, then back up | State transitions correctly |
| V6 | Trigger network error | User-friendly message displayed |
| V7 | Check error message clarity | All messages understandable |
| V8 | Follow recovery instructions | Each path resolves the issue |
| V9 | Reload after error | App returns to working state |
| V10 | Device validation | All tests pass on iPhone 16e |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../FEATURES_AND_IMPROVEMENTS.md) (A1, A2, R7, R8)
- [RISK_REGISTER.md](../RISK_REGISTER.md) (R7, R8)
- [Phase 7a](../phase-7a/README.md) (Preceding security phase)
- [Phase 7b](../phase-7b/README.md) (Preceding security phase)