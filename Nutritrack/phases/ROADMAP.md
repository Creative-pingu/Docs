# NutriTrack Development Roadmap

> **Last Updated**: 2026-08-17
> **Owner**: Architect Chat
> **Status**: Phase 10 complete, feedback incorporated

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
**Features**: Custom food export with complete schema, deployment improvements

### Phase 10: Recipe Management
**Status**: Complete
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
| 11 | Ready | Nutrition Tracking Enhancements |

---

## 🔗 DEPENDENCY CHAIN

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

---

## 🎯 RECOMMENDED NEXT STEPS

1. **NOW**: Implement Phase 11 (Nutrition Tracking Enhancements)
2. **After Phase 11**: Implement Phase 12 (Recipe and UX Improvements)
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