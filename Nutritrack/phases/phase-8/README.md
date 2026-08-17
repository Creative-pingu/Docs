# Phase 8 - Platform Reliability

> **Status**: ✅ Complete — deployed to `main` at v65 (2026-08-17). See [PHASE-REPORT-8.md](./PHASE-REPORT-8.md).
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Combines**: A1, A2, R7, R8
> **Depends on**: Phase 7 completion
> **ETC**: 2-3 hours

---

## Problem Statement
Current offline detection uses Worker /health endpoint with 4000ms timeout. `navigator.onLine` is unreliable on iOS standalone mode. Error messages are technical and not user-friendly. These issues affect user experience and reliability.

## Requirements

### Offline Detection (A1 / R7)
- [ ] Improve offline/online state detection reliability
- [ ] Implement hybrid approach: navigator.onLine + Worker fallback
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

## Proposed Solution

### Detection Strategy
```
1. Check navigator.onLine (fast, but unreliable on iOS)
2. If online: probe Worker /health with 1000ms timeout
3. If timeout/fail: check local cache state
4. If still uncertain: use last known state
```

### Error Message Mapping
| Current Technical Message | New User-Friendly Message | Recovery Path |
|----------------------------|---------------------------|--------------|
| network: fetch failed | No internet connection. Some features limited. | Check connection, retry |
| worker_502: notion_unreachable | Recipe sync unavailable. Try again later. | Wait, retry later |
| foods.json fetch failed: 404 | Food database missing. Please reload app. | Reload page |
| SW registration failed | App update failed. Continue offline. | None needed |
| Storage error | Data save failed. Please retry. | Retry action |

## Acceptance Criteria
- [ ] Offline state detected accurately within 2 seconds
- [ ] All error messages are user-friendly and actionable
- [ ] Recovery paths are clear for each error type
- [ ] No data loss during connectivity transitions
- [ ] Debug mode separate from production (optional)
- [ ] Error logs stored locally for diagnostics

## Technical Notes
- No new runtime dependencies
- Error messages: Centralized error handler component
- Timeout: Reduce from 4000ms to 1000ms for Worker health check
- Error logging: localStorage key `nt-error-logs` (array of objects)
- Feature detection: Use `navigator.onLine` with fallback

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Disable WiFi in iOS Settings | App detects offline within 2s |
| V2 | Enable airplane mode | Offline state updates correctly |
| V3 | Re-enable connectivity | App recovers gracefully |
| V4 | Worker down, then back up | State transitions correctly |
| V5 | Trigger network error | User-friendly message displayed |
| V6 | Check error message clarity | All messages understandable |
| V7 | Follow recovery instructions | Each path resolves the issue |
| V8 | Reload after error | App returns to working state |
| V9 | Device validation | All tests pass on iPhone 16e |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../FEATURES_AND_IMPROVEMENTS.md) (A1, A2, R7, R8)
- [RISK_REGISTER.md](../RISK_REGISTER.md) (R7, R8)
- [Phase 7a](../phase-7a/README.md) (Preceding security phase)
- [Phase 7b](../phase-7b/README.md) (Preceding security phase)