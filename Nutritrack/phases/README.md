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
├── phase-7a/
│   └── README.md
├── phase-7b/
│   └── README.md
├── phase-8/
│   └── README.md
├── phase-9/
│   └── README.md
├── phase-10/
│   └── README.md
├── phase-11/
│   └── README.md
├── phase-12/
│   └── README.md
├── phase-13/
│   └── README.md
├── phase-14/
│   └── README.md
└── phase-15/
    └── README.md

## Current Phases

| Phase | Status | Description | Priority | Depends On |
|-------|--------|-------------|----------|------------|
| **6m** | Complete | SW Update Banner Fix | - | - |
| **6n** | Complete | Multi-Select Food Logging | - | 6m |
| **7a** | Complete | Remove Anthropic API Call | CRITICAL | - |
| **7b** | Complete | Remove eval() Usage | CRITICAL | - |
| **8** | Ready | Platform Reliability | High | 7a, 7b |
| **6o** | Ready | Custom Food Manual Promotion | Medium | 6m |
| **9** | Queued | Storage and Performance | Medium | 8 |
| **10** | Queued | Deployment and Infrastructure | Medium | 9 |
| **11** | Queued | Testing and Future Features | Low | 15 |
| **12** | Queued | Recipe Management | High | 10 |
| **13** | Queued | Goal Tracking Enhancements | High | 12 |
| **14** | Queued | Quantity and Settings UX | Medium | 13 |
| **15** | Queued | Database Architecture | Medium | 14 |

## Execution Priority

**CRITICAL (Must Complete First - Blocks All Other Phases):**
- Phase 7a: Remove Anthropic API Call (S1/R11) - COMPLETE
- Phase 7b: Remove eval() Usage (S2/R12) - COMPLETE

**After Phase 7 Complete:**
- Phase 8: Platform Reliability (A1, A2, R7, R8) - READY
- Phase 6o: Custom Food Manual Promotion - READY

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
**Status**: Complete
**Brief**: [phase-7a/README.md](./phase-7a/README.md)
**Issue**: S1/R11 - Privacy violation resolved

### Phase 7b: Remove eval() Usage (CRITICAL)
**Status**: Complete
**Brief**: [phase-7b/README.md](./phase-7b/README.md)
**Issue**: S2/R12 - XSS vulnerability resolved

### Phase 8: Platform Reliability
**Status**: Ready for Implementation
**Brief**: [phase-8/README.md](./phase-8/README.md)
**Combines**: A1 (Offline Detection) + A2 (Error Handling UX) + R7 + R8
**Features**: Improved offline detection, user-friendly errors, offline mode toggle

### Phase 6o: Custom Food Manual Promotion
**Status**: Ready for Implementation
**Brief**: [phase-6o/README.md](./phase-6o/README.md)
**Decision**: A8 resolved - Use null placeholders for missing subtype fields

### Phase 9: Storage and Performance
**Status**: Queued
**Batches**: A3, R1, R5, R9
**Features**: localStorage vs IndexedDB, storage health, performance monitoring

### Phase 10: Deployment and Infrastructure
**Status**: Queued
**Batches**: A4, A5, R4, R10
**Features**: Deployment topology, versioning, GitHub Pages, SW updates

### Phase 11: Testing and Future Features
**Status**: Queued
**Batches**: A6, A7, A8
**Features**: Automated testing, voice input, custom food subtypes
**Note**: Moved to end as non-essential

### Phase 12: Recipe Management
**Status**: Queued
**Features**: Editable recipe templates, update ingredients/quantities

### Phase 13: Goal Tracking Enhancements
**Status**: Queued
**Features**: Colour coding, info tooltips, WHO vs optimal nutrition

### Phase 14: Quantity and Settings UX
**Status**: Queued
**Features**: Standard servings, round numbers, settings cleanup

### Phase 15: Database Architecture
**Status**: Queued
**Features**: Regional databases, standardized product names

## Suggested Execution Order

```
Phase 7a (CRITICAL) - COMPLETE
└── Phase 7b (CRITICAL) - COMPLETE
    
    Phase 8 (Platform Reliability) - READY
    ├── Phase 6o (Custom Food Manual Promotion) - READY
    │   
    └── Phase 9 (Storage and Performance)
        └── Phase 10 (Deployment and Infrastructure)
            └── Phase 12 (Recipe Management)
                └── Phase 13 (Goal Tracking)
                    └── Phase 14 (Quantity and Settings UX)
                        └── Phase 15 (Database Architecture)
                            └── Phase 11 (Testing and Future Features)
```

**Note**: Phase 7a and 7b are complete.
Phase 8 and 6o can run in parallel as they touch different feature areas.
Phase 6p has been removed (superseded by Phase 8).

## References
- [Project Charter](../PROJECT_CHARTER.md)
- [Development Process](../DEVELOPMENT_PROCESS.md)
- [Features and Improvements](../FEATURES_AND_IMPROVEMENTS.md)
- [Risk Register](../RISK_REGISTER.md)