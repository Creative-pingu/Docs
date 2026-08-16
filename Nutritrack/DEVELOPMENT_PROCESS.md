# NutriTrack — Development Process

> **Last Updated**: 2026-08-14
> **Owner**: Architect Chat

---

## Phase Lifecycle

```
[Brief] -> [Implement] -> [Review] -> [Device Validation] -> [Deploy]
```

## Decision-Making Process

1. **Architect** produces brief for proposed change
2. **Dev** implements the phase
3. **Dev** runs initial validation
4. **Review** verifies brief against current code state AND checks implementation
5. **Nick** performs device validation on iPhone 16e
6. **Iterate** on failures
7. **Success** unblocks next phase

No batching of features. One deliverable per phase, one validation matrix.

## Review Process (Post-Implementation)

After Developer has finished implementation:
1. **Code Quality Review**: Review Chat checks code for bugs and inefficiencies
2. **Check Passes**: Review Chat verifies all checks/tests are passing
3. **Security Review**: Review Chat identifies and flags any security issues
4. **Brief Verification**: Review Chat verifies brief against current code state
5. **No Architect Work Review**: Review Chat does NOT check Architect Chat work (briefs, designs, decisions)
6. **Approval**: Once all above are satisfied, implementation is approved for device validation

## Validation Discipline

**Matrix Cycle** (must pass on iPhone 16e against harness URL):
1. Perform the action
2. Confirm the visible UI state
3. Close the Safari tab
4. Reopen the harness URL
5. Verify the state has persisted correctly

> WARNING: "Works in this session" is NOT acceptable evidence.

## Release Management
- Direct commits to main only (no branches or PRs)
- Always bump CACHE_VERSION in sw.js on every deploy touching precached assets
- Deploy to production after successful device validation
- Two environments: Production (NutriTrack/) and harness (Nutritrack-proxy/)

## Change Control

| Change Type | Process |
|-------------|---------|
| Urgent bug fixes | Can bypass full process if critical (data loss, corruption) |
| Scope changes | Require Product Owner approval |
| Architectural decisions | Require Architect chat approval |
| New dependencies | Require Architect decision |

## Roles

| Role | Responsibilities | Owner |
|------|------------------|-------|
| Product Owner | Defines requirements, prioritizes features, accepts deliverables, makes scope decisions | Nick |
| Architect | Designs solutions, produces briefs, makes architectural decisions, evaluates implicit choices | Architect Chat |
| Developer | Implements phases, runs initial validation, creates implementation summaries | Dev Chat |
| Reviewer | Reviews code quality, checks all passes, identifies security issues, verifies brief against code state (does NOT review architect work) | Review Chat |
| Tester | Performs device validation on iPhone 16e using validation matrices | Nick |
| Council | Multi-perspective analysis for major decisions | Council Session |

---

## References
- [Project Charter](../PROJECT_CHARTER.md)
- [Risk Register](../RISK_REGISTER.md)