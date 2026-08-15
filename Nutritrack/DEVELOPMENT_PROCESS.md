# NutriTrack — Development Process

> **Last Updated**: 2026-08-14
> **Owner**: Architect Chat

---

## Phase Lifecycle

```
[Brief] -> [Review] -> [Implement] -> [Initial Validation] -> [Device Validation] -> [Deploy]
```

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
- Always bump CACHE_VERSION in sw.js when touching precached assets
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
| Product Owner | Defines requirements, prioritizes, accepts deliverables | Nick |
| Architect | Designs solutions, produces briefs, makes decisions | Architect Chat |
| Developer | Implements phases, initial validation | Dev Chat |
| Reviewer | Reviews code for bugs, inefficiencies, security | Review Chat |
| Tester | Device validation on iPhone 16e | Nick |
| Council | Multi-perspective analysis | Council Session |