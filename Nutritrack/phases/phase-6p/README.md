# Phase 6p - Offline Detection and Error UX

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Combines**: A1 (Offline Detection) + A2 (Error Handling UX)
> **Depends on**: Phase 6m completion

---

## Problem Statement
Current offline detection uses Worker /health endpoint with 4000ms timeout. navigator.onLine is unreliable on iOS standalone mode. Error messages are technical and not user-friendly.

## Requirements

### Offline Detection (A1)
- Improve offline/online state detection reliability
- Consider: User-explicit offline mode toggle (Settings)
- Consider: Hybrid approach (navigator.onLine + Worker fallback)
- Consider: Local caching of last known state
- Timeout should be configurable or optimized

### Error Handling UX (A2)
- Replace technical error messages with user-friendly ones
- Provide clear recovery instructions
- Separate debug build from production build
- Add error logging for post-mortem analysis (optional, local-only)

## Current Implementation Issues
- Worker /health endpoint: 4000ms timeout may be too long
- navigator.onLine: Unreliable on iOS PWA standalone mode
- Error messages: Too technical for end users

## Proposed Solution

### Detection Strategy
1. Check navigator.onLine (fast, but unreliable on iOS)
2. If online: probe Worker /health with shorter timeout (1000ms)
3. If timeout/fail: check local cache state
4. Provide user toggle to force offline mode

### Error Message Examples
| Current | New |
|---------|-----|
| network: fetch failed | No internet connection. Some features limited. |
| worker_502: notion_unreachable | Recipe sync unavailable. Try again later. |
| foods.json fetch failed: 404 | Food database missing. Please reload app. |

## Acceptance Criteria
- Offline state detected accurately within 2 seconds
- User can manually toggle offline mode
- All error messages are user-friendly
- Recovery paths are clear and actionable
- No data loss during connectivity transitions

## Technical Notes
- No new runtime dependencies
- Offline toggle: localStorage key nt-offline-mode
- Error messages: Centralized error handler component
- Timeout: Reduce from 4000ms to 1000ms for Worker health check

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Disable WiFi in iOS Settings | App detects offline within 2s |
| V2 | Enable airplane mode | Offline state updates correctly |
| V3 | Toggle offline mode in Settings | App respects manual override |
| V4 | Trigger network error | User-friendly message displayed |
| V5 | Re-enable connectivity | App recovers gracefully |
| V6 | Worker down, then back up | State transitions correctly |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../../FEATURES_AND_IMPROVEMENTS.md) (A1, A2)
- [RISK_REGISTER.md](../../RISK_REGISTER.md) (R7, R8)
- [Phase 6m](../phase-6m/README.md)