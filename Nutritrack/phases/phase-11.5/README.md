# Phase 11.5 - Infrastructure: Build & Deployment Automation

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Priority**: **CRITICAL** (Blocks Phase 12)
> **ETC**: 2-4 days
> **Structure**: Two parallel workstreams (R22 and R23)
> **Depends on**: Phase 11 completion
> **Blocks**: Phase 12 and all subsequent phases

---

## Overview

This phase addresses **critical infrastructure risks** (R22, R23) identified during the Phase 11 implementation review and test environment creation. The test environment required **4 separate fix commits** to resolve path configuration issues, and multiple incidents of source/compiled file divergence were observed.

**This phase MUST be completed before Phase 12 begins.**

---

## Problem Statement

### R22: Deployment Path Configuration Risk (CRITICAL)
Hardcoded deployment paths in multiple files cause Service Worker scope mismatches and fetch failures.

### R23: Manual Build Process Risk (HIGH)
Manual JSX to JS compilation leads to source/compiled divergence.

---

## Requirements

### R22 Requirements: Deployment Path Configuration
- [ ] Extract all deployment paths into single configuration
- [ ] Use relative paths where possible
- [ ] Ensure Service Worker scope matches deployment path exactly
- [ ] Automated path consistency test
- [ ] Documentation of deployment requirements

### R23 Requirements: Automated Build Process
- [ ] Automated Babel compilation (NutriTrack.jsx to NutriTrack.js)
- [ ] Pre-commit hook for build and consistency verification
- [ ] Build validation (Babel parse, Node syntax check)
- [ ] Documentation of build process

---

## Proposed Solution Architecture

### For R22: Path Configuration
**Option A: Configuration File (Recommended)**
All files import from a single deploy-config.js with basePath and other paths.

**Option B: Build-Time Injection**
Replace placeholders during build based on target environment.

**Option C: Relative Paths**
Use relative paths (./foods.json) instead of absolute.

### For R23: Automated Build
**build.js** script using Babel with pre-commit hook.

---

## Implementation Plan

### Week 1: R22 Mitigation (Target: 2026-08-27)
- Days 1-2: Create config, update all files
- Days 3-4: Create consistency tests, validate

### Week 2: R23 Mitigation (Target: 2026-09-03)
- Days 5-6: Create build script, add validation
- Days 7-8: Create pre-commit hook, test, document

---

## Validation Matrix

| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Run build script | NutriTrack.js generated successfully |
| V2 | Modify NutriTrack.jsx and commit | Pre-commit hook runs build automatically |
| V3 | Try to commit with inconsistent files | Commit rejected, error message shown |
| V4 | Deploy to test environment | All paths correct, app loads food database |
| V5 | Deploy to production environment | All paths correct, app works |
| V6 | Create new test environment | Single commit, no path fixes needed |
| V7 | Check path consistency test | All paths verified, test passes |
| V8 | Device validation on iPhone 16e | All tests pass on both environments |

---

## Acceptance Criteria

### For R22
- [ ] All deployment paths extracted to configuration
- [ ] No hardcoded absolute paths in source files
- [ ] Automated test verifies path consistency
- [ ] New test environment can be created with a single commit
- [ ] Documentation updated with deployment requirements
- [ ] All existing functionality preserved

### For R23
- [ ] Automated build script created and tested
- [ ] Pre-commit hook prevents commits with source/compiled divergence
- [ ] Build script runs successfully
- [ ] Documentation updated with build instructions
- [ ] All existing functionality preserved

---

## Technical Notes

- No new runtime dependencies required (Babel is build-time only)
- Service Worker caching behavior must be preserved
- Offline functionality must continue to work
- All existing paths must be updated atomically

## References

- [R22: Deployment Path Configuration Risk](../../../RISK_REGISTER.md#r22-deployment-path-configuration-risk)
- [R23: Manual Build Process Risk](../../../RISK_REGISTER.md#r23-manual-build-process-risk)
- [Test Environment Fix Report](../../../Test%20environment/README.md)
- [Development Process - Deployment Checklist](../../../DEVELOPMENT_PROCESS.md#deployment-checklist)
- [Development Process - Build Automation Requirements](../../../DEVELOPMENT_PROCESS.md#build-automation-requirements)