# NutriTrack Development Roadmap

> **Last Updated**: 2026-08-17
> **Owner**: Architect Chat
> **Status**: Phase 8 complete, Phase 9 moved to end

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

---

### Phase 6o: Custom Food Manual Promotion
**Status**: Ready for Implementation
**Batches**: A8
**Features**: Custom food export with complete schema, null placeholders

### Phase 10: Deployment and Infrastructure
**Status**: Queued
**Batches**: A4, A5, R4, R10
**Features**: Deployment topology, versioning, GitHub Pages, SW updates

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

### Phase 9: Storage and Performance
**Status**: Queued (moved to end - monitor only unless issues arise)
**Batches**: A3, R1, R5, R9
**Features**: localStorage vs IndexedDB evaluation, storage health, performance monitoring

### Phase 11: Testing and Future Features
**Status**: Queued
**Batches**: A6, A7, A8
**Features**: Automated testing, voice input, custom food subtypes
**Note**: Non-essential, at end of roadmap

---

## 📊 COMPLETE PHASES

| Phase | Status | Description |
|-------|--------|-------------|
| 6m | Complete | SW Update Banner Fix |
| 6n | Complete | Multi-Select Food Logging |
| 7a | Complete | Remove Anthropic API Call |
| 7b | Complete | Remove eval() Usage |
| 8 | Complete | Platform Reliability |

---

## 📋 READY FOR IMPLEMENTATION

| Phase | Status | Description |
|-------|--------|-------------|
| 6o | Ready | Custom Food Manual Promotion |

---

## 🔗 DEPENDENCY CHAIN

```
Phase 7a (CRITICAL) - COMPLETE
└── Phase 7b (CRITICAL) - COMPLETE
    
    Phase 8 (Platform Reliability) - COMPLETE
    
    Phase 6o (Custom Food Manual Promotion) - READY
    └── Phase 10 (Deployment and Infrastructure)
        └── Phase 12 (Recipe Management)
            └── Phase 13 (Goal Tracking)
                └── Phase 14 (Quantity and Settings UX)
                    └── Phase 15 (Database Architecture)
                        └── Phase 9 (Storage and Performance)
                            └── Phase 11 (Testing and Future Features)
```

**Note**: Phase 7a, 7b, and 8 are complete.
Phase 6o is ready for implementation.
Phase 9 moved to end - will only address if storage issues arise.

---

## 🎯 RECOMMENDED NEXT STEPS

1. **NOW**: Implement Phase 6o (Custom Food Manual Promotion)
2. **After 6o**: Implement Phase 10 (Deployment and Infrastructure)
3. **Then**: Phase 12 → 13 → 14 → 15 → 9 → 11

---

## 📝 Notes

- All phases must pass device validation on iPhone 16e
- No new runtime dependencies without Architect approval
- Privacy principles must be maintained in all implementations
- Review Chat must verify all changes
- Phase 9 is monitoring-only unless storage issues emerge

## 🔗 References
- [Project Charter](../PROJECT_CHARTER.md)
- [Development Process](../DEVELOPMENT_PROCESS.md)
- [Features and Improvements](../FEATURES_AND_IMPROVEMENTS.md)
- [Risk Register](../RISK_REGISTER.md)