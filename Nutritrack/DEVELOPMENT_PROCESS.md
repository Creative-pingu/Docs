# NutriTrack - Development Process

> **Last Updated**: 2026-08-18
> **Owner**: Architect Chat
> **Status**: Updated with test/production page requirement

---

## Phase Lifecycle

```
[Brief] -> [Implement] -> [Review] -> [Device Validation] -> [Deploy to Test] -> [Production Deploy]
```

**IMPORTANT**: All deployments go to TEST page first, then to PRODUCTION after validation.

---

## Decision-Making Process

1. **Architect** produces brief for proposed change
2. **Dev** implements the phase
3. **Dev** runs initial validation
4. **Review** verifies brief against current code state AND checks implementation
5. **Nick** performs device validation on iPhone 16e against TEST page
6. **Iterate** on failures
7. **Success** unblocks next phase

No batching of features. One deliverable per phase, one validation matrix.

---

## Review Process (Post-Implementation)

After Developer has finished implementation:
1. **Code Quality Review**: Review Chat checks code for bugs and inefficiencies
2. **Check Passes**: Review Chat verifies all checks/tests are passing
3. **Security Review**: Review Chat identifies and flags any security issues
4. **Brief Verification**: Review Chat verifies brief against current code state
5. **No Architect Work Review**: Review Chat does NOT check Architect Chat work (briefs, designs, decisions)
6. **Approval**: Once all above are satisfied, implementation is approved for device validation

---

## Validation Discipline

**Matrix Cycle** (must pass on iPhone 16e):
1. Perform the action
2. Confirm the visible UI state
3. Close the Safari tab
4. Reopen the TEST page URL
5. Verify the state has persisted correctly

> WARNING: "Works in this session" is NOT acceptable evidence.
> **CRITICAL**: Always validate against TEST page, not production.

---

## Release Management

### Environments
**THREE ENVIRONMENTS** (as of 2026-08-18):

1. **Production**: https://creative-pingu.github.io/NutriTrack/
   - Stable, user-facing version
   - Only updated after full TEST validation
   - Users add to home screen from here
   - CACHE_VERSION: `nutritrack-vXX`

2. **Test**: https://creative-pingu.github.io/NutriTrack-test/
   - New deployments go here first
   - Used for device validation by Nick
   - Data is isolated from production (separate localStorage origin)
   - CACHE_VERSION: `nutritrack-vXX-test`

3. **Harness**: https://creative-pingu.github.io/Nutritrack-proxy/
   - Cloudflare Worker proxy for Notion integration
   - Used for testing recipe sync functionality

### Deployment Workflow
```
1. Dev Chat implements phase
2. Architect Chat reviews brief
3. Dev Chat deploys to TEST page
   - Commit to main branch
   - GitHub Pages builds TEST page automatically
   - Bump CACHE_VERSION with -test suffix
4. Nick validates on iPhone 16e against TEST page
   - Runs full validation matrix
   - Checks all acceptance criteria
   - Tests offline functionality
5. If validation FAILS:
   - Dev Chat fixes issues
   - Re-deploy to TEST page
   - Repeat validation
6. If validation PASSES:
   - Dev Chat deploys to PRODUCTION page
   - Bump CACHE_VERSION (remove -test suffix)
   - Update APP_VERSION in index.html
   - Commit to main branch
   - GitHub Pages builds PRODUCTION page
```

### Versioning
- **CACHE_VERSION in sw.js**:
  - Test: `nutritrack-vXX-test` (e.g., nutritrack-v62-test)
  - Production: `nutritrack-vXX` (e.g., nutritrack-v62)
- **APP_VERSION in index.html**:
  - Must match CACHE_VERSION without suffix
  - Both test and production use same APP_VERSION
- **Bump on every deploy** that touches precached assets

### Data Safety
- localStorage is scoped to origin + path
- `/NutriTrack/` and `/NutriTrack-test/` have separate storage
- Users can safely test without risking production data
- No data migration needed between environments

---

## Change Control

| Change Type | Process |
|-------------|---------|
| Urgent bug fixes | Can bypass full process if critical (data loss, corruption) |
| Scope changes | Require Product Owner approval |
| Architectural decisions | Require Architect chat approval |
| New dependencies | Require Architect decision |
| Environment changes | Require Architect chat approval |

---

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
- [Architect Handover](../../uploads/Architect_Handover_2026-08-18.md)