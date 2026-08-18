# NutriTrack Development Roadmap

> **Last Updated**: 2026-08-18
> **Owner**: Architect Chat
> **Status**: Phase 10 complete, Phase 11 ready with sub-parts

---

## 🚨 IMMEDIATE PRIORITY (BLOCKING ALL DEVELOPMENT)

### Phase 7: Security Hardening

| Phase | ID | Issue | Priority | ETC | Status |
|-------|----|-------|----------|-----|--------|
| 7a | S1/R11 | Remove Anthropic API Call | CRITICAL | 1-2h | Complete |
| 7b | S2/R12 | Remove eval() Usage | CRITICAL | 2-4h | Complete |

**BLOCKS**: All other phases until both 7a and 7b are complete and validated.

---

## 🎯 POST-SECURITY ROADMAP

### Phase 8: Platform Reliability
**Status**: Complete
**Batches**: A1, A2, R7, R8
**Features**: Improved offline detection, user-friendly errors

### Phase 9: Custom Food Promotion and Deployment
**Status**: Complete
**Combines**: Old Phase 6o + Old Phase 10
**Features**: Custom food export with complete schema, deployment improvements

### Phase 10: Recipe Management
**Status**: Complete
**Features**: Editable recipe templates, update ingredients/quantities

### Phase 11: Enhanced Tracking and UX Polish
**Status**: Ready for Implementation
**Structure**: Three sub-parts
- **11a**: Water and Alcohol Tracking (HIGHEST PRIORITY)
- **11b**: Goal Tracking Enhancements
- **11c**: Bug Fixes and UX Polish
**ETC**: 8-12 hours

### Phase 12: Quantity and Settings UX
**Status**: Queued
**Features**: Standard servings, round numbers, settings cleanup

### Phase 13: Database Architecture
**Status**: Queued
**Features**: Regional databases, standardized product names

### Phase 14: Storage and Performance
**Status**: Queued (monitor only unless issues arise)
**Batches**: A3, R1, R5, R9
**Features**: localStorage vs IndexedDB evaluation, storage health, performance monitoring

### Phase 15: Testing and Future Features
**Status**: Queued
**Batches**: A6, A7, A8
**Features**: Automated testing, voice input, custom food subtypes
**Note**: Non-essential

---

## 📊 COMPLETE PHASES

| Phase | Status | Description |
|-------|--------|-------------|
| 6m | Complete | SW Update Banner Fix |
| 6n | Complete | Multi-Select Food Logging |
| 7a | Complete | Remove Anthropic API Call |
| 7b | Complete | Remove eval() Usage |
| 8 | Complete | Platform Reliability |
| 9 | Complete | Custom Food Promotion and Deployment |
| 10 | Complete | Recipe Management |

---

## 📋 READY FOR IMPLEMENTATION

| Phase | Status | Description |
|-------|--------|-------------|
| 11 | Ready | Enhanced Tracking and UX Polish (11a, 11b, 11c) |

---

## 🔗 DEPENDENCY CHAIN

```
Phase 7a (CRITICAL) - COMPLETE
└── Phase 7b (CRITICAL) - COMPLETE
    
    Phase 8 (Platform Reliability) - COMPLETE
    
    Phase 9 (Custom Food + Deployment) - COMPLETE
    
    Phase 10 (Recipe Management) - COMPLETE
    
    Phase 11 (Enhanced Tracking + UX Polish) - READY
    │   ├── 11a: Water and Alcohol Tracking
    │   ├── 11b: Goal Tracking Enhancements
    │   └── 11c: Bug Fixes and UX Polish
    │
    └── Phase 12 (Quantity and Settings UX)
        └── Phase 13 (Database Architecture)
            └── Phase 14 (Storage and Performance)
                └── Phase 15 (Testing and Future Features)
```

**Note**: Phase 7a, 7b, 8, 9, and 10 are complete.
Phase 11 is ready for implementation with three sub-parts.
Phase 14 moved to end - will only address if storage issues arise.

---

## 🎯 RECOMMENDED NEXT STEPS

1. **NOW**: Implement Phase 11 (Enhanced Tracking and UX Polish)
   - Start with 11a (Water and Alcohol Tracking - highest priority)
   - Then 11b (Goal Tracking Enhancements)
   - Finally 11c (Bug Fixes and UX Polish)
2. **After Phase 11**: Implement Phase 12 (Quantity and Settings UX)
3. **Then**: Phase 13 → 14 → 15

---

## 📝 Notes

- All phases must pass device validation on iPhone 16e
- No new runtime dependencies without Architect approval
- Privacy principles must be maintained in all implementations
- Review Chat must verify all changes
- Phase 14 is monitoring-only unless storage issues emerge

## 🔗 References
- [Project Charter](../PROJECT_CHARTER.md)
- [Development Process](../DEVELOPMENT_PROCESS.md)
- [Features and Improvements](../FEATURES_AND_IMPROVEMENTS.md)
- [Risk Register](../RISK_REGISTER.md)