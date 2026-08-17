# NutriTrack — Features and Improvements

> **Last Updated**: 2026-08-17
> **Purpose**: Action items and bug tracking for discussion with Architect Chat
> **Owner**: Nick
> **Status**: Review steps, privacy focus, and decision-making process order IMPLEMENTED. **CRITICAL BUGS S1 & S2 RESOLVED**.

---

## ✅ RESOLVED CRITICAL SECURITY BUGS

### S1: Hardcoded External API Call (PRIVACY VIOLATION) - RESOLVED
**ID**: S1  
**Severity**: CRITICAL  
**Status**: RESOLVED - Fixed by Phase 7a  
**File**: NutriTrack.jsx  
**Function**: parseRecipesFromPasteText()

**Issue**: Made direct POST requests to Anthropic API with user pasted recipe text, transmitting user data externally without explicit consent.

**Violations**:
- PROJECT_CHARTER: No user data collected or transmitted without explicit action
- PROJECT_CHARTER: Local-Only Data Storage
- PROJECT_CHARTER: Privacy by Design

**Resolution**: 
- Removed all references to api.anthropic.com and claude-sonnet
- Replaced with local-only recipe text splitter
- Splits pasted text on markdown headings (#/##/###) or colon-terminated titles
- Uses existing on-device regex parser (parseIngredients)
- No network requests made; works fully offline

**Implementation**: Phase 7a commit d4bd6aac (2026-08-16)
**Verified**: No anthropic.com references in codebase
**Owner**: Dev Chat (Vibe Nuage Agent)
**ETC**: 1-2 hours (actual)

---

### S2: eval() Usage (XSS VULNERABILITY) - RESOLVED
**ID**: S2  
**Severity**: CRITICAL  
**Status**: RESOLVED - Fixed by Phase 7b  
**File**: index.html  
**Function**: loadApp()

**Issue**: Used eval() to execute Babel-transformed code fetched from network. Created remote code execution vulnerability.

**Resolution**:
- Removed entire fetch+Babel transform+eval pipeline
- Pre-compiled NutriTrack.jsx to NutriTrack.js at build time
- Load NutriTrack.js via static script tag
- Removed Babel CDN dependency from runtime
- Bumped CACHE_VERSION v60->v61 and APP_VERSION v60->v61

**Implementation**: Phase 7b commit 214e9c3 (2026-08-17)
**Follow-up fixes**: b7965a8, 6f16b15, 86f9bc5
**Verified**: No eval() calls in codebase, NutriTrack.js exists and is pre-cached
**Owner**: Dev Chat (Vibe Nuage Agent)
**ETC**: 2-4 hours (actual)

---

## ✅ Recently Implemented

These updates have been implemented in the documentation:

### 1. Review Process Updates
**Status**: IMPLEMENTED in DEVELOPMENT_PROCESS.md and PROJECT_CHARTER.md

After Developer has finished implementation:
1. **Code Quality Review**: Review Chat checks code for bugs and inefficiencies
2. **Check Passes**: Review Chat verifies all checks/tests are passing
3. **Security Review**: Review Chat identifies and flags any security issues
4. **Brief Verification**: Review Chat verifies brief against current code state
5. **No Architect Work Review**: Review Chat does NOT check Architect Chat work (briefs, designs, decisions)
6. **Approval**: Once all above are satisfied, implementation is approved for device validation

**Decision-Making Process Order Fixed**:
The flow is now: Architect -> Dev -> Review -> Device Validation -> Deploy
(Review happens AFTER Dev, not before)

Updated in:
- [DEVELOPMENT_PROCESS.md](./DEVELOPMENT_PROCESS.md)
- [PROJECT_CHARTER.md](./PROJECT_CHARTER.md)

---

## 2. Core Privacy Focus
**Status**: IMPLEMENTED in PROJECT_CHARTER.md

Added as fundamental principle:
- **Local-Only Data Storage**: System does NOT collect any user data; everything is stored locally on-device
- **No Telemetry**: No tracking, analytics, or external data transmission
- **Privacy by Design**: System architecture ensures no user data leaves the device
- **Privacy Constraint**: NO user data collection, tracking, or external transmission

Updated in:
- [PROJECT_CHARTER.md](./PROJECT_CHARTER.md) (Executive Summary, Core Focus Areas, Architecture Principles, Privacy Guarantees)
- [RISK_REGISTER.md](./RISK_REGISTER.md) (Added Privacy to Risk Tolerance)

---

## 🔴 Action Items for Architect Discussion

These items still require architectural decisions or reevaluation.

### High Priority (Reevaluation Required)

| ID | Item | Current State | Owner | Notes |
|----|------|---------------|-------|-------|
| A1 | Offline Detection Mechanism | Worker health endpoint | Architect | Current approach: probes Worker /health endpoint with 4000ms timeout. navigator.onLine unreliable on iOS standalone mode. |
| A2 | Error Handling UX | In-app indicators | Dev | Current: technical error messages. Need more user-friendly messages and recovery paths. |

### Medium Priority

| ID | Item | Current State | Owner | Notes |
|----|------|---------------|-------|-------|
| A3 | localStorage vs IndexedDB | localStorage (5MB cap) | Architect | Monitor for issues. Migration path defined if needed. |
| A4 | Deployment Topology | GitHub Pages | Architect | Open to reconsideration if better options exist. |
| A5 | Versioning Strategy | CACHE_VERSION in sw.js | Architect | Improve if better semantic versioning needed. |

### Low Priority / Future Considerations

| ID | Item | Current State | Owner | Notes |
|----|------|---------------|-------|-------|
| A6 | Automated Testing | Manual device testing | Dev | Future consideration as codebase grows. |
| A7 | Voice Input | Deferred | Nick | Needs Anthropic account decision. **BLOCKED by S1/S2 privacy concerns - NOW UNBLOCKED**. |
| A8 | Custom Food Subtype Fields | Not yet briefed | Nick | **BLOCKS Phase 6o**: Should exported JSON patch include null placeholders or manual fill? |

---

## 📋 Open Questions for Architect

### Q1: Offline Detection (A1)
Current approach uses Worker health endpoint due to navigator.onLine unreliability.
**Options**:
- Add user-explicit offline mode toggle
- Use combination of navigator.onLine with Worker fallback
- Implement local caching of last known state
**Recommendation**: ?

### Q2: Error Handling (A2)
Current in-app indicators may be too technical.
**Options**:
- Add user-friendly error messages with clear recovery instructions
- Separate debug build from production build
- Add error logging for post-mortem analysis
**Recommendation**: ?

### Q3: Storage Migration (A3)
localStorage works for current scale but has limitations.
**Trigger for IndexedDB migration**:
- Sustained storage usage >4.5MB
- Performance issues with localStorage operations
- Need for transaction support
**Recommendation**: Monitor current usage, migrate if issues arise

### Q4: Custom Food Subtype Fields (A8)
Custom foods lack subtype fields (fibre subtypes, fat subtypes, amino acids).
**Options for JSON patch export**:
- Include null placeholders for all missing subtype fields
- Let Nick fill them in manually before applying patch
**Your Decision Needed**: This blocks Phase 6o briefing

---

## 📝 Implementation Notes

### For Architect Chat
- Review action items above (A1, A2, A3, A4, A5)
- Provide recommendations for A1 and A2
- Answer Q3 and Q4
- Update this document with decisions

### For Dev Chat
- Address high priority bugs B1 and B2
- Review medium priority items M1-M5
- Consider low priority items L1-L3 for code quality

### For Nick
- Review and prioritize action items
- Make decision on Q4 (subtype fields) to unblock Phase 6o
- Discuss A1 and A2 with Architect Chat
- Note: Review process, privacy focus, and decision-making process order are now implemented in documentation
- Note: S1 and S2 are now RESOLVED

---

## ✅ Completed
- [x] Review process documentation updated (no architect work review)
- [x] Core privacy focus added (local-only storage, no data collection)
- [x] Decision-making process order fixed (Review now after Dev steps)
- [x] All documentation uploaded to Docs/Nutritrack/
- [x] First comprehensive code review completed (2026-08-16)
- [x] Critical security bugs S1 and S2 resolved (2026-08-17)

---

## References

- [Project Charter](./PROJECT_CHARTER.md)
- [Risk Register](./RISK_REGISTER.md)
- [Architecture Documentation](./ARCHITECTURE.md)
- [Development Process](./DEVELOPMENT_PROCESS.md)
- [Dev Plan v6](../../uploads/Dev%20Plan%20v6.md)
- [Current Status](../../uploads/Current%20Status%202026-05-28.md)

---

Document History:
- 2026-08-14: Initial action items document created
- 2026-08-14: Updated with implemented changes (review process, privacy focus)
- 2026-08-14: Updated with decision-making process order fix
- 2026-08-16: Added critical security bugs S1 and S2 - BLOCKING
- 2026-08-17: **Marked S1 and S2 as RESOLVED - Phase 7a and 7b completed**
- Next review: After Architect discussion