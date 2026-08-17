# NutriTrack - Features and Improvements

> **Last Updated**: 2026-08-17
> **Purpose**: Action items and bug tracking for discussion with Architect Chat
> **Owner**: Nick
> **Status**: Review steps, privacy focus, and decision-making process order IMPLEMENTED. CRITICAL BUGS IDENTIFIED - BLOCKING.

---

## 🚨 CRITICAL SECURITY BUGS (IMMEDIATE ACTION REQUIRED)

These are blocking issues that violate core privacy principles and must be fixed before any further development.

### S1: Hardcoded External API Call (PRIVACY VIOLATION)
ID: S1
Severity: CRITICAL
Status: COMPLETE
File: NutriTrack.jsx
Function: parseRecipesFromPasteText()

Issue: Made direct POST requests to Anthropic API with user pasted recipe text, transmitting user data externally without explicit consent.

Fix: Function removed, replaced with local-only parsing.

Owner: Dev Chat -> Architect Chat
Blocks: All phases until resolved
ETC: 1-2 hours

---

### S2: eval() Usage (XSS VULNERABILITY)
ID: S2
Severity: CRITICAL
Status: COMPLETE
File: index.html
Function: loadApp()

Issue: Used eval to execute Babel-transformed code fetched from network. Creates remote code execution vulnerability.

Fix: Pre-compiled JSX to JS during build/deploy.

Owner: Dev Chat -> Architect Chat
Blocks: All phases until resolved
ETC: 2-4 hours

---

## ✅ Recently Implemented

These updates have been implemented in the documentation:

### 1. Review Process Updates
**Status**: IMPLEMENTED in DEVELOPMENT_PROCESS.md and PROJECT_CHARTER.md

After Developer has finished implementation:
1. Code Quality Review: Review Chat checks code for bugs and inefficiencies
2. Check Passes: Review Chat verifies all checks/tests are passing
3. Security Review: Review Chat identifies and flags any security issues
4. Brief Verification: Review Chat verifies brief against current code state
5. No Architect Work Review: Review Chat does NOT check Architect Chat work
6. Approval: Once all above are satisfied, implementation is approved for device validation

**Decision-Making Process Order Fixed**: Architect -> Dev -> Review -> Device Validation -> Deploy

Updated in:
- [DEVELOPMENT_PROCESS.md](./DEVELOPMENT_PROCESS.md)
- [PROJECT_CHARTER.md](./PROJECT_CHARTER.md)

---

### 2. Core Privacy Focus
**Status**: IMPLEMENTED in PROJECT_CHARTER.md

Added as fundamental principle:
- Local-Only Data Storage: System does NOT collect any user data; everything is stored locally on-device
- No Telemetry: No tracking, analytics, or external data transmission
- Privacy by Design: System architecture ensures no user data leaves the device
- Privacy Constraint: NO user data collection, tracking, or external transmission

Updated in:
- [PROJECT_CHARTER.md](./PROJECT_CHARTER.md)
- [RISK_REGISTER.md](./RISK_REGISTER.md)

---

## 📋 ACTION ITEMS FOR ARCHITECT DISCUSSION

### High Priority (Reevaluation Required)

| ID | Item | Current State | Owner | Notes | Phase |
|----|------|---------------|-------|-------|-------|
| A1 | Offline Detection Mechanism | Worker health endpoint | Architect | navigator.onLine unreliable on iOS | Phase 8 |
| A2 | Error Handling UX | In-app indicators | Dev | Need user-friendly messages | Phase 8 |
| A3 | localStorage vs IndexedDB | localStorage (5MB cap) | Architect | Monitor for issues | Phase 14 |
| A4 | Deployment Topology | GitHub Pages | Architect | Open to reconsideration | Phase 9 |
| A5 | Versioning Strategy | CACHE_VERSION in sw.js | Architect | Improve if better semantic versioning | Phase 9 |

### Medium Priority

| ID | Item | Current State | Owner | Notes | Phase |
|----|------|---------------|-------|-------|-------|
| A6 | Automated Testing | Manual device testing | Dev | Future consideration | Phase 15 |
| A7 | Voice Input | Deferred | Nick | Needs Anthropic account decision | Phase 15 |
| A8 | Custom Food Subtype Fields | Null placeholders decided | Nick | Implemented in Phase 9 | Phase 9 |

---

## 🆕 NEW FEATURE IDEAS (Not Yet Prioritized)

### Nutrition Tracking Enhancements
| ID | Idea | Priority | Phase |
|----|------|----------|-------|
| F3 | Colour coding when exceeding recommended daily intake | High | Phase 11 |
| F4 | Info button explaining why exceeding limits is harmful | High | Phase 11 |
| F5 | Nutrition options: Recommended (WHO base levels) | Medium | Phase 11 |
| F6 | Nutrition options: Optimal (personalized by activity level) | Medium | Phase 11 |
| F13 | Track water consumption | High | Phase 11 |
| F14 | Traffic light system for healthy/not healthy nutrients | High | Phase 11 |
| F22 | Alcohol tracking with adjustable percentages | Medium | Phase 11 |

### Recipe and UX Improvements
| ID | Idea | Priority | Phase |
|----|------|----------|-------|
| F1 | Recipes act as templates editable when logged | High | Phase 12 |
| F2 | Edit recipes: update ingredients (quantities, etc.) | High | Phase 12 |
| F7 | Quantity options include standard servings (cloves, tsp, etc.) | Medium | Phase 12 |
| F8 | Round numbers (14.333333g -> 14.3g or 14g) | Medium | Phase 12 |
| F9 | Settings: Remove name field (individual user) | Low | Phase 12 |
| F10 | Settings: Explain why inputs are needed (info icons) | Low | Phase 12 |
| F15 | After selecting food, options should reset | High | Phase 12 |
| F16 | Multi-select with checkboxes directly on items | High | Phase 12 |
| F17 | Bigger button to change dates | Medium | Phase 12 |
| F18 | Fix page zoom/centering glitch | Medium | Phase 12 |
| F19 | Fix back navigation in recipe ingredient selection | Medium | Phase 12 |
| F20 | Remove source from recipe tab | Low | Phase 12 |
| F21 | Fix scroll to bottom going too far | Medium | Phase 12 |

### Database Architecture
| ID | Idea | Priority | Phase |
|----|------|----------|-------|
| F11 | One base database + downloadable regional food databases | Medium | Phase 13 |
| F12 | Standardized products grouped with alternate names | Low | Phase 13 |

---

## 📝 IMPLEMENTATION NOTES

### For Architect Chat
- Please review action items above (A1-A8)
- Provide recommendations for A1 and A2
- Update this document with decisions

### For Nick
- Review and prioritize new feature ideas (F1-F22)
- Make decisions on feature priority
- Note: Review process, privacy focus, and decision-making process order are now implemented

---

## ✅ Completed
- [x] Review process documentation updated (no architect work review)
- [x] Core privacy focus added (local-only storage, no data collection)
- [x] Decision-making process order fixed (Review now after Dev steps)
- [x] All documentation uploaded to Docs/Nutritrack/
- [x] Phase 7a: Remove Anthropic API Call
- [x] Phase 7b: Remove eval() Usage
- [x] Phase 8: Platform Reliability
- [x] Phase 9: Custom Food Promotion and Deployment
- [x] Phase 10: Recipe Management

---

## References

- [Project Charter](./PROJECT_CHARTER.md)
- [Risk Register](./RISK_REGISTER.md)
- [Architecture Documentation](./ARCHITECTURE.md)
- [Development Process](./DEVELOPMENT_PROCESS.md)
- [Dev Plan v6](../../uploads/Dev%20Plan%20v6.md)
- [Current Status](../../uploads/Current%20Status%202026-05-28.md)