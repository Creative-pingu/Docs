# NutriTrack - Development Process

> **Last Updated**: 2026-08-20
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

## Lessons Learned from Test Environment Creation

> **Source**: Phase 11 Review - App Reviewer Skill, 2026-08-20

The NutriTrack-test environment creation (2026-08-18 to 2026-08-20) revealed critical gaps in our deployment and build processes. What should have been a simple repository copy required 6 commits and exposed 4 distinct failure classes.

### Key Lessons

1. **Path Configuration is Not Optional**
   - Hardcoded paths in multiple files caused cascading failures
   - Service Worker scope MUST match deployment path exactly
   - Fetch calls MUST be within SW scope or use relative paths

2. **Version Bumping Must Be Atomic**
   - SHELL_APP_VERSION (index.html) and CACHE_VERSION (sw.js) must change together
   - A mismatch causes old Service Worker to serve stale cached assets

3. **Source and Compiled Must Stay in Sync**
   - Manual compilation leads to divergence
   - This caused runtime errors and inconsistent behavior

4. **Test on Actual Deployment, Not Just Local**
   - Path issues invisible when testing locally
   - Only manifest when deployed to GitHub Pages

5. **Service Worker Caching Can Mask Issues**
   - Stale SW can serve old cached assets
   - Debugging requires unregistering old SW and hard refreshing

### Prevention Strategies

- [x] Documented deployment path for each repository
- [x] Created Test Environment Fix Report with debugging checklist
- [ ] TODO: Automate path configuration (R22)
- [ ] TODO: Automate build process (R23)

---

## Deployment Checklist

> **Purpose**: Prevent R22 (deployment path configuration) failures
> **Use**: Before deploying to ANY environment

### Pre-Deployment
- [ ] All fetch paths use relative paths OR correct deployment prefix
- [ ] SW registration path matches deployment path
- [ ] SW scope matches deployment path
- [ ] All precached assets use correct paths
- [ ] index.html manifest/icon paths use correct prefix
- [ ] SHELL_APP_VERSION matches CACHE_VERSION
- [ ] Build-info comment matches versions

### Environment-Specific
**Production**:
- [ ] Paths prefixed with /NutriTrack/ or relative
- [ ] SW at /NutriTrack/sw.js, scope /NutriTrack/
- [ ] CACHE_VERSION = nutritrack-vXX

**Test**:
- [ ] Paths prefixed with /NutriTrack-test/ or relative
- [ ] SW at /NutriTrack-test/sw.js, scope /NutriTrack-test/
- [ ] CACHE_VERSION = nutritrack-vXX-test

### Post-Deployment
- [ ] Verify SW registered with correct scope in DevTools
- [ ] Hard refresh to ensure fresh load
- [ ] Test food database loads
- [ ] Test critical user flows

---

## Build Automation Requirements

> **Purpose**: Prevent R23 (manual build process) failures
> **Use**: Before committing NutriTrack.jsx changes

### Requirements
1. Automated Compilation - NutriTrack.jsx to NutriTrack.js must be automatic
2. Consistency Verification - Pre-commit hook verifies source/compiled match
3. Validation - Compiled JS passes Babel parse and Node syntax checks

### Minimum Viable Solution
```javascript
// build.js
const babel = require("@babel/core");
const fs = require("fs");
const result = babel.transformFileSync("NutriTrack.jsx", {
  presets: ["@babel/preset-react", "@babel/preset-env"]
});
fs.writeFileSync("NutriTrack.js", result.code);
```

```bash
# .git/hooks/pre-commit
if git diff --cached --name-only | grep -q "NutriTrack.jsx"; then
  node build.js || exit 1
fi
```

---

## References
- [Project Charter](../PROJECT_CHARTER.md)
- [Risk Register](../RISK_REGISTER.md)
- [Architect Handover](../../uploads/Architect_Handover_2026-08-18.md)
- [Test Environment Fix Report](../../Test%20environment/README.md)