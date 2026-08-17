# NutriTrack Development Roadmap

> **Last Updated**: 2026-08-17
> **Owner**: Architect Chat
> **Status**: Phase 7a/7b ready for implementation

---

## 🚨 IMMEDIATE PRIORITY (BLOCKING ALL DEVELOPMENT)

### Phase 7: Security Hardening

| Phase | ID | Issue | Priority | ETC | Status |
|-------|----|-------|----------|-----|--------|
| 7a | S1/R11 | Remove Anthropic API Call | CRITICAL | 1-2h | Ready |
| 7b | S2/R12 | Remove eval() Usage | CRITICAL | 2-4h | Ready |

**BLOCKS**: All other phases until both 7a and 7b are complete and validated.

---

## 🎯 POST-SECURITY ROADMAP

### Phase 8: Platform Reliability
**Batches**: A1, A2, R7, R8

| Task | ID | Description | Priority |
|------|----|-------------|----------|
| Offline Detection | A1/R7 | Improve offline/online state detection | High |
| Error Handling UX | A2/R8 | User-friendly error messages with recovery paths | High |
| User Toggle | - | Explicit offline mode switch | High |

**ETC**: 3-5 hours
**Depends on**: Phase 7 completion

---

### Phase 9: Storage & Performance
**Batches**: A3, R1, R5, R9

| Task | ID | Description | Priority |
|------|----|-------------|----------|
| Storage Migration | A3 | localStorage vs IndexedDB evaluation | Medium |
| Corruption Guard | R1/R5 | Storage health instrumentation | Medium |
| Performance Monitor | R9 | Performance degradation tracking | Medium |

**ETC**: 4-6 hours
**Depends on**: Phase 8 completion

---

### Phase 10: Deployment & Infrastructure
**Batches**: A4, A5, R4, R10

| Task | ID | Description | Priority |
|------|----|-------------|----------|
| Deployment Topology | A4 | GitHub Pages vs alternatives | Medium |
| Versioning Strategy | A5 | Improve semantic versioning | Medium |
| GitHub Pages Downtime | R4 | Monitor and mitigate | Medium |
| SW Update Failures | R10 | Debug instrumentation | Medium |

**ETC**: 3-5 hours
**Depends on**: Phase 9 completion

---

### Phase 12: Recipe Management
**Batches**: New feature requests

| Task | Description | Priority |
|------|-------------|----------|
| Recipe Templates | Recipes editable when logged (remove unused ingredients) | High |
| Edit Recipes | Update ingredients and quantities | High |

**ETC**: 4-6 hours
**Depends on**: Phase 10 completion

---

### Phase 13: Goal Tracking Enhancements
**Batches**: New feature requests

| Task | Description | Priority |
|------|-------------|----------|
| Colour Coding | Visual indicators when exceeding daily intake | High |
| Info Tooltips | Explain why exceeding limits is harmful | High |
| Nutrition Options | Recommended (WHO base) and Optimal (personalized) | Medium |

**ETC**: 3-5 hours
**Depends on**: Phase 12 completion

---

### Phase 14: Quantity & Settings UX
**Batches**: New feature requests

| Task | Description | Priority |
|------|-------------|----------|
| Standard Servings | Quantity options include cloves, tsp, etc. | Medium |
| Round Numbers | Display 14.333333g as 14.3g or 14g | Medium |
| Settings Cleanup | Remove name field, add info tooltips | Low |

**ETC**: 2-4 hours
**Depends on**: Phase 13 completion

---

### Phase 15: Database Architecture
**Batches**: New feature requests

| Task | Description | Priority |
|------|-------------|----------|
| Regional Databases | Base DB + downloadable regional food databases | Medium |
| Standardized Products | Group alternate names (tipo 0 flour = plain flour) | Low |

**ETC**: 5-8 hours
**Depends on**: Phase 14 completion

---

### Phase 11: Testing & Future Features
**Batches**: A6, A7, A8

| Task | ID | Description | Priority |
|------|----|-------------|----------|
| Automated Testing | A6 | Framework setup | Low |
| Voice Input | A7 | Anthropic account decision | Low |
| Custom Food Subtypes | A8 | Null placeholders (DECIDED) | Low |

**ETC**: 5-8 hours
**Depends on**: Phase 15 completion

---

## 📊 COMPLETE PHASES

| Phase | Status | Description |
|-------|--------|-------------|
| 6m | Complete | SW Update Banner Fix |
| 6n | Complete | Multi-Select Food Logging |

---

## 📋 READY FOR IMPLEMENTATION

| Phase | Status | Description |
|-------|--------|-------------|
| 7a | Ready | Remove Anthropic API Call (CRITICAL) |
| 7b | Ready | Remove eval() Usage (CRITICAL) |
| 6o | Ready | Custom Food Manual Promotion |
| 6p | Ready | Offline Detection and Error UX |

---

## 🔗 DEPENDENCY CHAIN

```
Phase 7a (CRITICAL)
└── Phase 7b (CRITICAL)
    
    Phase 8 (Platform Reliability)
    └── Phase 9 (Storage & Performance)
        └── Phase 10 (Deployment & Infra)
            └── Phase 12 (Recipe Management)
                └── Phase 13 (Goal Tracking)
                    └── Phase 14 (Quantity & Settings UX)
                        └── Phase 15 (Database Architecture)
                            └── Phase 11 (Testing & Future Features)
```

**Note**: Phase 11 moved to end as non-essential.
Phase 6o and 6p can run in parallel after Phase 7 completes.

---

## 🎯 RECOMMENDED NEXT STEPS

1. **IMMEDIATE**: Implement Phase 7a and 7b (Security Hardening)
2. **After Phase 7**: Implement Phase 8 (Platform Reliability)
3. **Parallel**: Start Phase 6o and 6p after Phase 7 completes
4. **Sequential**: Phase 9 → 10 → 12 → 13 → 14 → 15 → 11

---

## 📝 Notes

- All phases must pass device validation on iPhone 16e
- No new runtime dependencies without Architect approval
- Privacy principles must be maintained in all implementations
- Review Chat must verify all changes

## 🔗 References
- [Project Charter](../PROJECT_CHARTER.md)
- [Development Process](../DEVELOPMENT_PROCESS.md)
- [Features and Improvements](../FEATURES_AND_IMPROVEMENTS.md)
- [Risk Register](../RISK_REGISTER.md)