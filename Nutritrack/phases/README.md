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
└── phase-6p/
    └── README.md

## Current Phases

| Phase | Status | Description | Depends On |
|-------|--------|-------------|------------|
| **6m** | Complete | SW Update Banner Fix | - |
| **6n** | Ready | Multi-Select Food Logging | 6m |
| **6o** | Ready | Custom Food Manual Promotion | 6m, A8 |
| **6p** | Ready | Offline Detection and Error UX | 6m |

## Phase Details

### Phase 6m: SW Update Banner Fix
**Status**: Complete (deployed)
**Iterations**: 6m-1 through 6m-4
**Result**: Service worker update banner now triggers correctly

### Phase 6n: Multi-Select Food Logging
**Status**: Ready for Implementation
**Brief**: [phase-6n/README.md](./phase-6n/README.md)
**Features**:
- One meal picker for entire batch
- Per-food quantity entry with last-logged pre-fill
- Explicit toggle to enter multi-select mode

### Phase 6o: Custom Food Manual Promotion
**Status**: Ready for Implementation
**Brief**: [phase-6o/README.md](./phase-6o/README.md)
**Decision**: A8 resolved - Use null placeholders for missing subtype fields
**Features**:
- Custom food export with complete schema
- All 25+ nutrient fields included (value or null)
- JSON patch compatible with foods.json v1

### Phase 6p: Offline Detection and Error UX
**Status**: Ready for Implementation
**Brief**: [phase-6p/README.md](./phase-6p/README.md)
**Combines**: A1 (Offline Detection) + A2 (Error Handling UX)
**Features**:
- Improved offline/online state detection
- User-explicit offline mode toggle
- User-friendly error messages with recovery paths

## Suggested Execution Order

6m (DONE)
├── 6n: Multi-Select Food Logging
├── 6o: Custom Food Manual Promotion
└── 6p: Offline Detection and Error UX

**Note**: 6n, 6o, and 6p can run in parallel as they touch different feature areas.

## References
- [Project Charter](../PROJECT_CHARTER.md)
- [Development Process](../DEVELOPMENT_PROCESS.md)
- [Features and Improvements](../FEATURES_AND_IMPROVEMENTS.md)
- [Risk Register](../RISK_REGISTER.md)