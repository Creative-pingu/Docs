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

---

## Observations from Phase 7a, 7b, 8, and 9

### Phase 7a (Remove Anthropic API Call)
- Implementation: Local-only recipe text splitter using markdown headings and colon detection
- Approach: Splits on #/##/### or lines ending with colon, collects following lines as ingredients
- Parsing: Uses existing parseIngredients/parseIngredientLine utilities
- Servings: Extracts from (serves N) or (servings N) patterns
- Fallback: Treats entire paste as one recipe if no structure detected
- Observation: Default servings is 4 if not specified

### Phase 7b (Remove eval() Usage)
- Implementation: Pre-compiled NutriTrack.jsx to NutriTrack.js at build time
- Build: Uses Babel with preset-react (classic runtime) + preset-env (modules:false)
- Loading: Static script tag injection, no dynamic evaluation
- Architecture: Both JSX (source) and JS (compiled) exist in repo
- Observation: SHELL_APP_VERSION rename was necessary to avoid const collision between index.html and NutriTrack.js

### Phase 8 (Platform Reliability)
- Implementation: Hybrid offline detection + centralized error handling
- Offline Detection: navigator.onLine fast gate + Worker /health probe (1000ms timeout) + localStorage cached state
- Error Handling: mapError()/friendlyError() translate technical messages to user-friendly strings
- Error Logging: Local-only nt-error-logs (capped at 50 entries, never transmitted) with read-only viewer
- 502/503 Handling: Treated as sync outage (not connectivity) - isOnline stays true, sync UI surfaces error
- Versioning: CACHE_VERSION v61 -> v62, APP_VERSION v61 -> v62
- Device Validation: All Phase 8 validation matrix tests passed on iPhone 16e (2026-08-17)

### Phase 9 (Custom Food Promotion and Deployment)
- Implementation: Custom food editing + JSON patch export + schema migration + SW update fallback
- Custom Food Editing: openEditCustomFood() loads existing food, saveCustomFood updates in-place, Edit button on each food
- Form Modes: Simple (6 standard package values) / Advanced (all 19 NUTRIENT_META fields), toggle with clear UX
- Schema Migration: migrateCustomFoods() one-time backfill of all numeric nutrient keys to null for legacy records (idempotent)
- JSON Patch Export: buildCustomFoodPatch() emits RFC 6902 add-ops envelope with schema_version 1, all 33 fields present
- Schema Mapping: CUSTOM_FOOD_TO_DB covers all nutrient fields (macros, fibre/fat subtypes, amino acids, micronutrients)
- Settings Integration: Custom Foods management card with active count + Manage button
- SW Update Fallback: 4-second controllerchange timeout fallback ensures users never stranded on stale build
- Versioning: CACHE_VERSION v65 -> v66 -> v67, APP_VERSION v65 -> v66 -> v67
- Device Validation: All Phase 9 validation matrix tests passed on iPhone 16e (2026-08-17)

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