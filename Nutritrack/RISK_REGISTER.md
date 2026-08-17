# NutriTrack — Risk Register

> **Last Updated**: 2026-08-17
> **Owner**: Architect Chat
> **Status**: Active

---

## Overview

This document tracks all identified risks for the NutriTrack project.

**Risk Tolerance Summary**:
- Acceptable: App may break occasionally (non-critical features)
- NOT Acceptable: Data loss or corruption (ZERO TOLERANCE)
- NOT Acceptable: Unauthorized data transmission (ZERO TOLERANCE)

---

## Active Risks

### Critical Risks (Require Immediate Attention)

| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R1 | localStorage corruption | Data Integrity | Low | High | Storage health instrumentation, corrupted keys guard | Nick | Monitor |
| R2 | iOS Safari quirks | Platform | Medium | Medium | Feature detection, fallback mechanisms | Dev Chat | Active |
| R5 | Data loss on save-on-load | Data Integrity | Low | High | Corrupted keys guard | Review Chat | Monitor |

### High Priority (Reevaluation Required)

_No risks currently in this tier._ R7 and R8 were resolved by Phase 8 (2026-08-17); see Resolved Risks below.

### Monitored Risks

| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R3 | CDN dependency failures | External | Low | Medium | Local fallbacks, monitor status | Dev Chat | Monitor |
| R4 | GitHub Pages downtime | Deployment | Low | Medium | Monitor status | Nick | Monitor |
| R6 | Notion API changes | Integration | Low | Low | Worker buffer, paste fallback | Architect | Monitor |
| R9 | App performance degradation | Performance | Medium | Medium | Performance monitoring | Dev Chat | Monitor |
| R10 | SW update failures | Platform | Medium | Medium | SW debug instrumentation | Architect | Monitor |

### Resolved Risks

| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R11 | Hardcoded external API call (Anthropic) | Privacy/Security | High | High | Removed function, replaced with local-only parsing (Phase 7a) | Dev Chat | **RESOLVED - 2026-08-16** |
| R12 | eval() usage in code execution | Security | High | High | Pre-compiled JSX to JS, removed eval() (Phase 7b) | Dev Chat | **RESOLVED - 2026-08-17** |
| R7 | Offline detection reliability | Platform | Medium | High | Hybrid navigator.onLine + Worker /health 1000ms probe + last-known cache (Phase 8) | Dev Chat | **RESOLVED - 2026-08-17** |
| R8 | Error handling UX | UX | Medium | Medium | Centralized friendlyError() mapping + local-only nt-error-logs + recovery paths (Phase 8) | Dev Chat | **RESOLVED - 2026-08-17** |

---

## Observations from Phase 7a and 7b

### Phase 7a (Remove Anthropic API Call)
- **Implementation**: Local-only recipe text splitter using markdown headings and colon detection
- **Approach**: Splits on #/##/### or lines ending with colon, collects following lines as ingredients
- **Parsing**: Uses existing parseIngredients/parseIngredientLine utilities
- **Servings**: Extracts from (serves N) or (servings N) patterns
- **Fallback**: Treats entire paste as one recipe if no structure detected
- **Observation**: Default servings is 4 if not specified - reasonable but could be configurable

### Phase 7b (Remove eval() Usage)
- **Implementation**: Pre-compiled NutriTrack.jsx to NutriTrack.js at build time
- **Build**: Uses Babel with preset-react (classic runtime) + preset-env (modules:false)
- **Loading**: Static script tag injection, no dynamic evaluation
- **Architecture**: Both JSX (source) and JS (compiled) exist in repo
- **Observation**: SHELL_APP_VERSION rename was necessary to avoid const collision between index.html and NutriTrack.js
- **Observation**: Dynamic script injection pattern used (proven from earlier commits)
- **Observation**: Babel CDN removed from runtime, but React/ReactDOM still loaded from CDN

### Phase 8 (Platform Reliability — A1/A2/R7/R8)
- **Offline detection (R7)**: Hybrid strategy — navigator.onLine fast gate + Worker /health probe with 1000ms timeout (down from 4000ms) + last-known state cached in localStorage (nt-last-online). A transient probe failure falls back to cached state rather than flipping to offline (flicker-free); a genuine outage is confirmed on the next 5s poll.
- **502/503 semantics**: A Worker 502/503 (Notion upstream unreachable) is treated as a sync outage, not connectivity — isOnline stays true so the sync UI surfaces the error instead of mis-reporting the device as offline.
- **Error UX (R8)**: Centralized mapError()/friendlyError() translate technical messages to short, actionable strings; every UI error path routes through it. Local-only nt-error-logs ring buffer (capped 50, never transmitted) with a read-only viewer in Settings > About.
- **Observation**: The error-mapping smoke test caught an ordering bug — "foods.json fetch failed: 404" contains "fetch failed" and would have mis-classified a DB load failure as a network error. fooddb is now checked before network (more specific patterns first).
- **Observation**: The offline banner's position:sticky (in a document.body portal) did not reliably pin on iOS; switched to position:fixed at top with an in-flow spacer of equal height to avoid header overlap.
- **Observation**: "Clear logs" originally called setView("settings") while already on settings (a no-op); fixed with an errorLogsVersion re-render counter bumped on clear/inject.
- **Debug tooling**: Added inject-test-error buttons in Developer > Debug tools (one per error type) so the friendly-message + log pipeline can be exercised entirely offline without the Notion buttons (which stay greyed out offline).

### New Considerations
| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R13 | Build process dependency | Build | Low | Medium | Document build steps, consider GitHub Actions | Dev Chat | Monitor |
| R14 | Source/artifact divergence | Maintenance | Low | Low | Keep NutriTrack.jsx as source of truth, regenerate JS on changes | Dev Chat | Monitor |

---

## References

- [Project Charter](./PROJECT_CHARTER.md)
- [Features and Improvements](./FEATURES_AND_IMPROVEMENTS.md)
- [Architecture Documentation](./ARCHITECTURE.md)