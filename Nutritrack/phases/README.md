# Phase Documentation

This folder contains briefs and documentation for each phase of NutriTrack development.

## Folder Structure

phases/
├── README.md
├── phase-6m/
│   └── README.md
├── phase-6n/
│   └── README.md
├── phase-6o/
│   └── README.md
├── phase-6p/
│   └── README.md
├── phase-7a/
│   └── README.md
└── phase-7b/
    └── README.md

## Current Phases

| Phase | Status | Description | Priority | Depends On |
|-------|--------|-------------|----------|------------|
| **6m** | Complete | SW Update Banner Fix | - | - |
| **6n** | Complete | Multi-Select Food Logging | - | 6m |
| **7a** | Complete | Remove Anthropic API Call | CRITICAL | - |
| **7b** | Ready | Remove eval() Usage | CRITICAL | - |
| **6o** | Ready | Custom Food Manual Promotion | Medium | 6m |
| **6p** | Ready | Offline Detection and Error UX | Medium | 6m |

## Execution Priority

**CRITICAL (Must Complete First - Blocks All Other Phases):**
- Phase 7a: Remove Anthropic API Call (S1/R11)
- Phase 7b: Remove eval() Usage (S2/R12)

**After Phase 7 Complete:**
- Phase 6o: Custom Food Manual Promotion
- Phase 6p: Offline Detection and Error UX

## Phase Details

### Phase 6m: SW Update Banner Fix
**Status**: Complete (deployed)
**Iterations**: 6m-1 through 6m-4
**Result**: Service worker update banner now triggers correctly

### Phase 6n: Multi-Select Food Logging
**Status**: Complete (implemented)
**Brief**: [phase-6n/README.md](./phase-6n/README.md)
**Features**: One meal picker, per-food quantity entry, explicit toggle

### Phase 7a: Remove Anthropic API Call (CRITICAL)
**Status**: Complete (deployed to main, v60) — see [COMPLETION-REPORT.md](./phase-7a/COMPLETION-REPORT.md)
**Brief**: [phase-7a/README.md](./phase-7a/README.md)
**Issue**: S1/R11 - Privacy violation
**Blocks**: All other phases until resolved

### Phase 7b: Remove eval() Usage (CRITICAL)
**Status**: Ready for Implementation
**Brief**: [phase-7b/README.md](./phase-7b/README.md)
**Issue**: S2/R12 - XSS vulnerability
**Blocks**: All other phases until resolved

### Phase 6o: Custom Food Manual Promotion
**Status**: Ready for Implementation
**Brief**: [phase-6o/README.md](./phase-6o/README.md)
**Decision**: A8 resolved - Use null placeholders for missing subtype fields

### Phase 6p: Offline Detection and Error UX
**Status**: Ready for Implementation
**Brief**: [phase-6p/README.md](./phase-6p/README.md)
**Combines**: A1 (Offline Detection) + A2 (Error Handling UX)

## Suggested Execution Order

```
Phase 7a (CRITICAL - BLOCKING)
└── Phase 7b (CRITICAL - BLOCKING)
    
Phase 6o
└── Phase 6p
```

**Note**: Phase 7a and 7b must be completed before any other phases.
Phase 6o and 6p can run in parallel after Phase 7 completes.

## Full Roadmap (Post-Phase 7)

| Phase | Priority | Description | Batches |
|-------|----------|-------------|---------|
| **7a** | CRITICAL | Remove Anthropic API Call | Security |
| **7b** | CRITICAL | Remove eval() Usage | Security |
| **8** | High | Offline Detection & Error UX | Platform |
| **9** | Medium | Storage Migration & Performance | Storage |
| **10** | Medium | Deployment & Versioning | Infra |
| **11** | Low | Testing & Future Features | Future |

## References
- [Project Charter](../PROJECT_CHARTER.md)
- [Development Process](../DEVELOPMENT_PROCESS.md)
- [Features and Improvements](../FEATURES_AND_IMPROVEMENTS.md)
- [Risk Register](../RISK_REGISTER.md)