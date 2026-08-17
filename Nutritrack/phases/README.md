# Phase Documentation

This folder contains briefs and documentation for each phase of NutriTrack development.

## Current Phases

| Phase | Status | Description | Priority |
|-------|--------|-------------|----------|
| **6m** | Complete | SW Update Banner Fix | - |
| **6n** | Complete | Multi-Select Food Logging | - |
| **7a** | Complete | Remove Anthropic API Call | CRITICAL |
| **7b** | Complete | Remove eval() Usage | CRITICAL |
| **8** | Complete | Platform Reliability | High |
| **9** | Complete | Custom Food Promotion and Deployment | Medium |
| **10** | Complete | Recipe Management | High |
| **11** | Ready | Nutrition Tracking Enhancements | High |
| **12** | Queued | Recipe and UX Improvements | High |
| **13** | Queued | Database Architecture | Medium |
| **14** | Queued | Storage and Performance | Low |
| **15** | Queued | Testing and Future Features | Low |

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
**Status**: Complete
**Brief**: [phase-8/README.md](./phase-8/README.md)
**Combines**: A1 (Offline Detection) + A2 (Error Handling UX) + R7 + R8
**Features**: Improved offline detection, user-friendly errors

### Phase 9: Custom Food Promotion and Deployment
**Status**: Complete
**Brief**: [phase-9/README.md](./phase-9/README.md)
**Combines**: Old Phase 6o + Old Phase 10
**Features**: Custom food export with complete schema, deployment improvements

### Phase 10: Recipe Management
**Status**: Complete
**Brief**: [phase-10/README.md](./phase-10/README.md)
**Features**: Editable recipe templates, update ingredients/quantities

### Phase 11: Nutrition Tracking Enhancements
**Status**: Ready for Implementation
**Features**: F3, F4, F5, F6, F13, F14, F22
- Colour coding when exceeding daily intake
- Info button explaining why exceeding limits is harmful
- Nutrition options: Recommended (WHO base) and Optimal (personalized)
- Track water consumption
- Traffic light system for healthy/not healthy nutrients
- Alcohol tracking with adjustable percentages

### Phase 12: Recipe and UX Improvements
**Status**: Queued
**Features**: F1, F2, F7, F8, F9, F10, F15, F16, F17, F18, F19, F20, F21
- Recipes as editable templates
- Edit recipes: update ingredients/quantities
- Standard servings in quantity options
- Round numbers display
- Settings cleanup
- After selecting food, options should reset
- Multi-select with checkboxes directly on items
- Bigger date change button
- Fix page zoom/centering glitch
- Fix back navigation in recipe ingredient selection
- Remove source from recipe tab
- Fix scroll to bottom going too far

### Phase 13: Database Architecture
**Status**: Queued
**Features**: F11, F12
- One base database + downloadable regional food databases
- Standardized products grouped with alternate names

### Phase 14: Storage and Performance
**Status**: Queued (monitor only unless issues arise)
**Batches**: A3, R1, R5, R9
**Features**: localStorage vs IndexedDB evaluation, storage health, performance monitoring
**Note**: Moved to end as no high risks currently

### Phase 15: Testing and Future Features
**Status**: Queued
**Batches**: A6, A7, A8
**Features**: Automated testing, voice input, custom food subtypes
**Note**: Non-essential

## Suggested Execution Order

```
Phase 7a (CRITICAL) - COMPLETE
└── Phase 7b (CRITICAL) - COMPLETE
    
    Phase 8 (Platform Reliability) - COMPLETE
    
    Phase 9 (Custom Food + Deployment) - COMPLETE
    
    Phase 10 (Recipe Management) - COMPLETE
    
    Phase 11 (Nutrition Tracking) - READY
    └── Phase 12 (Recipe and UX Improvements)
        └── Phase 13 (Database Architecture)
            └── Phase 14 (Storage and Performance)
                └── Phase 15 (Testing and Future Features)
```

**Note**: Phase 7a, 7b, 8, 9, and 10 are complete.
Phase 11 is ready for implementation.
Phase 14 moved to end - will only address if storage issues arise.

## References
- [Project Charter](../PROJECT_CHARTER.md)
- [Development Process](../DEVELOPMENT_PROCESS.md)
- [Features and Improvements](../FEATURES_AND_IMPROVEMENTS.md)
- [Risk Register](../RISK_REGISTER.md)