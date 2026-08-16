# Phase 7a - Remove Anthropic API Call (CRITICAL - PRIVACY VIOLATION)

> **Status**: Ready for Implementation
> **Priority**: CRITICAL - BLOCKS ALL PHASES
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Issue**: S1 / R11
> **ETC**: 1-2 hours

---

## Problem Statement
The function `parseRecipesFromPasteText()` in NutriTrack.jsx makes direct POST requests to the Anthropic API (api.anthropic.com) with user-pasted recipe text. This violates the core privacy principles defined in PROJECT_CHARTER: "No user data collected or transmitted without explicit action" and "Local-Only Data Storage".

## Security Impact
- **Privacy Violation**: User data (recipe text) transmitted externally without consent
- **Compliance Risk**: Violates project charter privacy guarantees
- **Data Leakage**: User content sent to third-party API

## Requirements

### Must Do
- [ ] Remove `parseRecipesFromPasteText()` function entirely from NutriTrack.jsx
- [ ] Remove all references to api.anthropic.com
- [ ] Remove Anthropic API key/credentials if present

### Should Do
- [ ] Replace with local-only recipe parsing (if needed)
- [ ] Ensure paste functionality still works without external calls
- [ ] Verify no other external API calls exist in codebase

## Code Locations
- **Primary**: NutriTrack.jsx - `parseRecipesFromPasteText()` function
- **Search for**: All occurrences of `api.anthropic.com` and `anthropic.com`

## Acceptance Criteria
- [ ] No network requests to anthropic.com domains
- [ ] No user data transmitted externally
- [ ] Recipe paste functionality works locally or is removed
- [ ] All tests pass
- [ ] Device validation on iPhone 16e passes

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Search codebase for anthropic.com | No results found |
| V2 | Paste recipe text | No external network call made |
| V3 | Check Network tab in DevTools | No requests to anthropic.com |
| V4 | Use app offline | Full functionality without errors |
| V5 | Device validation | All privacy checks pass |

## Technical Notes
- No new dependencies required
- Consider: Local recipe parsing using built-in logic
- If recipe parsing is needed, implement client-side only
- Verify all imports/requires for Anthropic SDK are removed

## References
- [PROJECT_CHARTER.md](../../PROJECT_CHARTER.md) (Privacy Guarantees)
- [FEATURES_AND_IMPROVEMENTS.md](../../FEATURES_AND_IMPROVEMENTS.md) (S1)
- [RISK_REGISTER.md](../../RISK_REGISTER.md) (R11)