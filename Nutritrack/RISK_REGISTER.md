# NutriTrack — Risk Register

> **Last Updated**: 2026-08-22
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

### Monitored Risks

| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R3 | CDN dependency failures | External | Low | Medium | Local fallbacks, monitor status | Dev Chat | Monitor |
| R4 | GitHub Pages downtime | Deployment | Low | Medium | App continues to work offline (Phase 9 mitigation) | Nick | MONITOR - 2026-08-17 |
| R6 | Notion API changes | Integration | Low | Low | Worker buffer, paste fallback | Architect | Monitor |
| R7 | Offline detection reliability | Platform | Medium | High | Hybrid detection: navigator.onLine + Worker /health probe with 1000ms timeout + cached state (Phase 8) | Dev Chat | MONITOR - 2026-08-17 |
| R8 | Error handling UX | UX | Medium | Medium | Centralized mapError()/friendlyError() with user-friendly messages + local-only error logs (Phase 8) | Dev Chat | MONITOR - 2026-08-17 |
| R9 | App performance degradation | Performance | Medium | Medium | Performance monitoring | Dev Chat | Monitor |
| R10 | SW update failures | Platform | Medium | Medium | SW update banner with 4s controllerchange timeout fallback (Phase 9) | Architect | MONITOR - 2026-08-17 |
| R15 | Stale cached online state | Platform | Low | Medium | nt-last-online cached state may become stale on cold start after long inactivity. Mitigation: Fallback to probe on first load, cached state used only during probe | Dev Chat | Monitor |
| R16 | Error log storage failure | Storage | Low | Low | Error logs stored in nt-error-logs (capped at 50). Mitigation: try-catch on all localStorage operations, graceful degradation if storage full | Dev Chat | Monitor |
| R17 | Error message pattern drift | Maintenance | Low | Low | mapError() uses regex pattern matching. Mitigation: Specific patterns checked first (foods.json before network), default fallback to generic message | Dev Chat | Monitor |
| R18 | Custom food schema drift | Data Integrity | Low | Medium | Custom food export uses schema v1. Mitigation: Export validates against foods.json schema; manual review recommended before merging patches | Dev Chat | Monitor |
| R19 | Migration failure mid-process | Data Integrity | Low | High | One-time migrateCustomFoods() backfill. Mitigation: Idempotent operation, only runs when needed, persists on success | Dev Chat | Monitor |
| R20 | JSON patch application errors | Deployment | Low | Medium | Manual patch merge process. Mitigation: RFC 6902 format with clear instructions; maintainer must verify before applying | Dev Chat | Monitor |
| R21 | Custom food ID collisions | Data Integrity | Low | Medium | custom_* prefix used for IDs. Mitigation: ID format preserved in export; maintainer should check for collisions before merging | Dev Chat | Monitor |

### Resolved Risks

| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R11 | Hardcoded external API call (Anthropic) | Privacy/Security | High | High | Removed function, replaced with local-only parsing (Phase 7a) | Dev Chat | RESOLVED - 2026-08-16 |
| R12 | eval() usage in code execution | Security | High | High | Pre-compiled JSX to JS, removed eval() (Phase 7b) | Dev Chat | RESOLVED - 2026-08-17 |

### R22: Deployment Path Configuration Risk

**SEVERITY: CRITICAL** | **STATUS: RESOLVED - 2026-08-22**

Hardcoded deployment paths in multiple files have been centralized in deploy-config.js with automated validation.

- **Resolution**: All path definitions moved to deploy-config.js; check-paths.js validates consistency; pre-commit hooks prevent divergence
- **Implementation**: Validated hardcoded paths approach - source files contain paths that must match config values
- **Owner**: Dev Chat
- **Evidence**: Commits 9596232e (NutriTrack), 1a938389 (NutriTrack-test)

### R23: Manual Build Process Risk

**SEVERITY: HIGH** | **STATUS: RESOLVED - 2026-08-22**

Manual JSX to JS compilation has been automated with pre-commit validation.

- **Resolution**: build.js automates Babel compilation; pre-commit hook runs build + validation; source/compiled divergence now impossible
- **Owner**: Dev Chat
- **Evidence**: build.js, .husky/pre-commit, scripts/check-paths.js in both repos

### New Considerations
| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R13 | Build process dependency | Build | Low | Medium | Document build steps, consider GitHub Actions | Dev Chat | Monitor |
| R14 | Source/artifact divergence | Maintenance | High | Medium | Keep NutriTrack.jsx as source of truth, regenerate JS on changes. CRITICAL: Multiple Phase 11 incidents show this is a recurring high-risk issue requiring automation | Dev Chat | MONITOR - 2026-08-20 |

---

## Observations from Phase 7a, 7b, 8, and 9

### Phase 7a (Remove Anthropic API Call)
