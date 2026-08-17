# NutriTrack Development Roadmap

> **Last Updated**: 2026-08-17
> **Owner**: Architect Chat
> **Status**: Phase 9 complete

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
**Combines**: Phase 6o (Custom Food) + Phase 10 (Deployment)
**Features**: Custom food export with complete schema, edit + simple/advanced form, settings entry, SW update-failure handling, v67

### Phase 10: Recipe Management
**Status**: Queued
**Features**: Editable recipe templates, update ingredients/quantities

### Phase 11: Goal Tracking Enhancements
**Status**: Queued
**Features**: Colour coding, info tooltips, WHO vs optimal nutrition

### Phase 12: Quantity and Settings UX
**Status**: Queued
**Features**: Standard servings, round numbers, settings cleanup

### Phase 13: Database Architecture
**Status**: Queued
**Features**: Regional databases, standardized product names

### Phase 14: Storage and Performance
**Status**: Queued (moved to end - monitor only unless issues arise)
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

---

## 📋 READY FOR IMPLEMENTATION

_No phases currently ready for implementation._

---

## 🔗 DEPENDENCY CHAIN

```
Phase 7a (CRITICAL) - COMPLETE
└── Phase 7b (CRITICAL) - COMPLETE
    
    Phase 8 (Platform Reliability) - COMPLETE
    
    Phase 9 (Custom Food + Deployment) - COMPLETE
    └── Phase 10 (Recipe Management)
        └── Phase 11 (Goal Tracking)
            └── Phase 12 (Quantity and Settings UX)
                └── Phase 13 (Database Architecture)
                    └── Phase 14 (Storage and Performance)
                        └── Phase 15 (Testing and Future Features)
```

**Note**: Phase 7a, 7b, 8, and 9 are complete.
Next up is Phase 10 (Recipe Management).
Phase 14 moved to end - will only address if storage issues arise.

---

## 🎯 RECOMMENDED NEXT STEPS

1. **NOW**: Implement Phase 10 (Recipe Management)
2. **Then**: Phase 11 → 12 → 13 → 14 → 15
3. **Then**: Phase 11 → 12 → 13 → 14 → 15

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