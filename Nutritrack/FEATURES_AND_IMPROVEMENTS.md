# NutriTrack — Features and Improvements

> **Last Updated**: 2026-08-14
> **Purpose**: Action items for discussion with Architect Chat
> **Owner**: Nick

---

## Action Items for Architect Discussion

These items require architectural decisions or reevaluation.

### 🔴 High Priority (Reevaluation Required)

| ID | Item | Current State | Owner | Notes |
|----|------|---------------|-------|-------|
| A1 | Offline Detection Mechanism | Worker health endpoint | Architect | Current approach: probes Worker `/health` endpoint with 4000ms timeout. `navigator.onLine` unreliable on iOS standalone mode. |
| A2 | Error Handling UX | In-app indicators | Dev | Current: technical error messages. Need more user-friendly messages and recovery paths. |

### 🟡 Medium Priority

| ID | Item | Current State | Owner | Notes |
|----|------|---------------|-------|-------|
| A3 | localStorage vs IndexedDB | localStorage (5MB cap) | Architect | Monitor for issues. Migration path defined if needed. |
| A4 | Deployment Topology | GitHub Pages | Architect | Open to reconsideration if better options exist. |
| A5 | Versioning Strategy | CACHE_VERSION in sw.js | Architect | Improve if better semantic versioning needed. |

### 🟢 Low Priority / Future Considerations

| ID | Item | Current State | Owner | Notes |
|----|------|---------------|-------|-------|
| A6 | Automated Testing | Manual device testing | Dev | Future consideration as codebase grows. |
| A7 | Voice Input | Deferred | Nick | Needs Anthropic account decision. |
| A8 | Custom Food Subtype Fields | Not yet briefed | Nick | **BLOCKS Phase 6o**: Should exported JSON patch include null placeholders or manual fill? |

---

## Open Questions for Architect

### Q1: Offline Detection (A1)
Current approach uses Worker health endpoint due to `navigator.onLine` unreliability.
**Options**:
- Add user-explicit offline mode toggle
- Use combination of `navigator.onLine` with Worker fallback
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

## Implementation Notes

### For Architect Chat
- Please review action items above
- Provide recommendations for A1 and A2
- Answer Q3 and Q4
- Update this document with decisions

### For Nick
- Review and prioritize action items
- Make decision on Q4 (subtype fields) to unblock Phase 6o
- Discuss A1 and A2 with Architect Chat

---

## References

- [Project Charter](./PROJECT_CHARTER.md)
- [Risk Register](./RISK_REGISTER.md)
- [Architecture Documentation](./ARCHITECTURE.md)
- [Dev Plan v6](../../uploads/Dev%20Plan%20v6.md)
- [Current Status](../../uploads/Current%20Status%202026-05-28.md)

---

> **Document History**
> - 2026-08-14: Initial action items document created
> - Next review: After Architect discussion
