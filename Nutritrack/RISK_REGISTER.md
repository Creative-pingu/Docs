# NutriTrack — Risk Register

> **Last Updated**: 2026-08-14
> **Owner**: Architect Chat
> **Status**: Active

---

## Overview

This document tracks all identified risks for the NutriTrack project.

**Risk Tolerance Summary**:
- Acceptable: App may break occasionally (non-critical features)
- NOT Acceptable: Data loss or corruption (ZERO TOLERANCE)

---

## Active Risks

### Critical Risks (Require Immediate Attention)

| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R1 | localStorage corruption | Data Integrity | Low | High | Storage health instrumentation, corrupted keys guard | Nick | Monitor |
| R2 | iOS Safari quirks | Platform | Medium | Medium | Feature detection, fallback mechanisms | Dev Chat | Active |
| R5 | Data loss on save-on-load | Data Integrity | Low | High | Corrupted keys guard | Review Chat | Monitor |

### High Priority (Reevaluation Required)

| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R7 | Offline detection reliability | Platform | Medium | High | Reevaluate current mechanism | Architect | Reevaluate |
| R8 | Error handling UX | UX | Medium | Medium | Reevaluate for better messages | Dev | Reevaluate |

### Monitored Risks

| ID | Risk | Category | Likelihood | Impact | Mitigation | Owner | Status |
|----|------|----------|------------|--------|------------|-------|--------|
| R3 | CDN dependency failures | External | Low | Medium | Local fallbacks, monitor status | Dev Chat | Monitor |
| R4 | GitHub Pages downtime | Deployment | Low | Medium | Monitor status | Nick | Monitor |
| R6 | Notion API changes | Integration | Low | Low | Worker buffer, paste fallback | Architect | Monitor |
| R9 | App performance degradation | Performance | Medium | Medium | Performance monitoring | Dev Chat | Monitor |
| R10 | SW update failures | Platform | Medium | Medium | SW debug instrumentation | Architect | Monitor |