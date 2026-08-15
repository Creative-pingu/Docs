# NutriTrack — Project Charter

> **Last Updated**: 2026-08-14  
> **Status**: Active Development  
> **Owner**: Nick (Creative-pingu)

---

## 1. Executive Summary

**Project Name**: NutriTrack  
**Purpose**: Personal nutritional tracking app for cycling tours through South America, also serving as a portfolio piece.  
**Primary User**: Nick (iPhone 16e)  
**Status**: Production live at `creative-pingu.github.io/NutriTrack/`  
**Development Model**: Iterative, as needed during tour  

### Success Criteria

- [x] Works reliably offline on iPhone 16e
- [x] All core features functional (logging, goals, recipes, exercise, supplements)
- [x] Data persists across app restarts and device reboots
- [ ] Production-ready quality (no known critical bugs)
- [ ] Portfolio-worthy codebase and documentation

---

## 2. Project Scope

### In Scope

- Nutritional tracking (food, recipes, supplements, exercise)
- Offline-first functionality
- Local data storage (localStorage with IndexedDB as future option)
- Notion sync as optional extra (not primary usage path)
- PWA with Add to Home Screen capability
- Data export/import functionality
- UI/UX improvements as needed during use

### Out of Scope

- Multi-user support
- Commercial monetization
- Android-specific optimizations (unless they conflict with iOS functionality)
- Backend services requiring maintenance (Cloudflare Worker for proxy is acceptable)

### Assumptions

- Primary platform: iPhone 16e with iOS Safari
- Connectivity: Intermittent (offline-capable by design)
- User: Technically competent (Nick)
- Maintenance: Active during tour, potentially beyond
- Budget: No recurring costs; one-off purchases <5 euro acceptable

### Constraints

- Budget: No recurring costs; one-off purchases <5 euro acceptable
- Dependencies: No new runtime dependencies without architect approval
- Deployment: GitHub Pages (current choice, open to reconsideration)
- Testing: Must pass device validation on iPhone 16e
- Browser Support: Primary focus on iPhone 16e Safari, but support other browsers

---

## 3. Architecture Principles

| Principle | Description |
|-----------|-------------|
| Offline-First | All core features work without connectivity |
| Local-First Data | User data stored on-device; sync is discrete action |
| Minimal Dependencies | Only essential libraries; CDN where possible |
| No Backend Maintenance | Only Cloudflare Worker for credential/CORS handling |
| Single JSX | React app in one file during development |
| Progressive Enhancement | Core functionality without JavaScript (where feasible) |

### Key Components

| Component | Technology | Purpose |
|-----------|------------|---------|
| Frontend | React PWA | User interface and application logic |
| Storage | localStorage (5MB cap) | On-device data persistence |
| Food Database | External foods.json (~342KB) | Nutritional data for 830 foods |
| Sync | Cloudflare Worker -> Notion API | Read-only recipe synchronization |
| Deployment | GitHub Pages | Hosting for production and test harness |
| Dependencies | React, Babel, Lucide, JSZip | Runtime libraries from CDN |

---

## 4. Roles and Responsibilities

| Role | Responsibilities | Current Owner |
|------|------------------|---------------|
| Product Owner | Defines requirements, prioritizes features, accepts deliverables, makes scope decisions | Nick |
| Architect | Designs solutions, produces briefs, makes architectural decisions, evaluates implicit choices | Architect Chat |
| Developer | Implements phases, runs initial validation, creates implementation summaries | Dev Chat |
| Reviewer | Reviews code for bugs, inefficiencies, and security issues | Review Chat |
| Tester | Performs device validation on iPhone 16e using validation matrices | Nick |
| Council | Multi-perspective analysis for major decisions | Council Session |

### Decision-Making Process

1. Architect produces brief for proposed change
2. Review verifies brief against current code state
3. Dev implements the phase
4. Dev runs initial validation
5. Nick performs device validation on iPhone 16e
6. Iterate on failures
7. Success unblocks next phase

No batching of features. One deliverable per phase, one validation matrix.

### Escalation Path

| Issue Type | Escalation |
|------------|------------|
| Technical disagreements | Architect chat |
| Security concerns | Review chat |
| Scope changes | Product Owner (Nick) |
| Major architectural decisions | Council session |

---

## 5. Development Process

### Phase Lifecycle

Brief -> Review -> Implement -> Initial Validation -> Device Validation -> Deploy

### Validation Discipline

Every phase must pass a device validation matrix on Nick iPhone 16e against the harness URL.

Matrix Cycle:
1. Perform the action
2. Confirm the visible UI state
3. Close the Safari tab
4. Reopen the harness URL
5. Verify the state has persisted correctly

WARNING: Works in this session is NOT acceptable evidence.

### Release Management
- Direct commits to main only (no branches or PRs)
- Always bump CACHE_VERSION in sw.js on every deploy touching precached assets
- Deploy to production after successful device validation
- Two environments: Production (NutriTrack/) and harness (Nutritrack-proxy/)

### Change Control

| Change Type | Process |
|-------------|---------|
| Urgent bug fixes | Can bypass full process if critical (data loss, corruption) |
| Scope changes | Require Product Owner approval |
| Architectural decisions | Require Architect chat approval |
| New dependencies | Require Architect decision |

---

## 6. Quality Standards

### Testing Requirements
- Every phase must pass device validation matrix
- Test on iPhone 16e Safari (primary)
- Support other browsers (secondary)
- Reload-and-check required for persistence claims

### Code Quality
- Follow existing code patterns and conventions
- No new dependencies without architect approval
- Security review by Review chat for all changes
- Code comments for non-obvious logic

### Documentation Standards
- Phase briefs required for all changes
- Validation matrices documented
- Architecture decisions recorded in ADRs
- Implementation summaries with inline-fix and flagged-for-follow-up lists

---

## 7. Risk Management

See [RISK_REGISTER.md](./RISK_REGISTER.md) for detailed risk tracking.

### Risk Tolerance

| Risk Category | Tolerance |
|---------------|-----------|
| App functionality | App may break occasionally (non-critical features) |
| Data integrity | ZERO TOLERANCE for data loss or corruption |
| Performance | App must remain usable offline |
| Connectivity | Must work offline; sync requires connectivity |

### Critical Risks
1. R1 - localStorage corruption (Monitor)
2. R2 - iOS Safari quirks (Active mitigation)
3. R5 - Data loss on save-on-load (Monitor with corrupted keys guard)

### Reevaluation Required
- Offline detection mechanism (Architect Chat)
- Error handling UX (Dev Chat)

---

## 8. Documentation Plan

### Repository Structure

Docs/
└── Nutritrack/
    ├── README.md
    ├── PROJECT_CHARTER.md
    ├── RISK_REGISTER.md
    ├── ARCHITECTURE.md
    ├── DATA_MODEL.md
    ├── DEVELOPMENT_PROCESS.md
    ├── USER_GUIDE.md
    ├── FEATURES_AND_IMPROVEMENTS.md
    ├── decisions/
    │   ├── README.md
    │   ├── ADR-001-localStorage.md
    │   └── ADR-002-no-backend.md
    └── phases/
        ├── README.md
        ├── phase-6m/
        │   └── README.md
        └── phase-6n/
            └── README.md

### Document Types

| Document | Purpose | Owner | Format |
|----------|---------|-------|--------|
| Project Charter | Project vision, scope, governance | Nick | Markdown |
| Risk Register | Active risks and mitigations | Architect | Markdown |
| ADRs | Architecture Decision Records | Architect | Markdown |
| Phase Briefs | Requirements for each phase | Architect | Markdown |
| Implementation Summaries | What was implemented | Dev | Markdown |
| Validation Matrices | Test results | Nick | Markdown |
| User Guide | How to use the app | Nick | Markdown |

### Documentation Ownership
- Content Owner: Nick (Product Owner)
- Implementation: Agents (Dev Chat, Architect Chat, etc.)
- Review: Review Chat (for technical accuracy)

---

## 9. Implicit Choices

| Decision | Choice | Rationale | Status |
|----------|--------|-----------|--------|
| Development Device | iPhone 16e focus | Primary user device | Active |
| Browser Support | Safari primary, others supported | iOS focus but cross-browser compatible | Active |
| Storage | localStorage (5MB cap) | Works for current scale; monitor for issues | Keep, raise if problematic |
| Deployment | GitHub Pages | Free, reliable, iOS-friendly | Active |
| Backend | No maintenance-requiring backend | Only Cloudflare Worker for proxy | Active |
| Data Sync | Notion via Worker (optional) | Extra feature, not primary usage path | Active |
| Offline Detection | Worker health endpoint | navigator.onLine unreliable on iOS | Reevaluate |
| Error Handling | In-app indicators | Current approach | Reevaluate |
| Testing | Manual device testing | Sufficient for current needs | Active |
| Versioning | CACHE_VERSION in sw.js | Current approach | Improve if better option exists |

---

## 10. Open Questions

| ID | Question | Owner | Status | Blocking |
|----|----------|-------|--------|----------|
| Q1 | Should we migrate from localStorage to IndexedDB? | Architect | Needs analysis | Future phases |
| Q2 | Is GitHub Pages the optimal deployment long-term? | Architect | Open | None |
| Q3 | Should we add automated testing? | Dev | Future consideration | None |
| Q4 | Voice input: Set up Anthropic account or accept limited parser? | Nick | Deferred | Phase 8 |
| Q5 | Custom food subtype fields: null placeholders or manual fill? | Nick | Blocks 6o brief | Phase 6o |

---

## 11. Glossary

| Term | Definition |
|------|------------|
| PWA | Progressive Web App - Web app that can be installed on device |
| SW | Service Worker - Background script for offline functionality |
| Phase | Single deliverable with its own validation matrix |
| Harness | Test environment at Nutritrack-proxy/ |
| ADR | Architecture Decision Record - Documentation of key decisions |
| CDN | Content Delivery Network - Hosted libraries (React, Babel, etc.) |

---

## 12. References

- [Dev Plan v6](../../uploads/Dev%20Plan%20v6.md)
- [Current Status](../../uploads/Current%20Status%202026-05-28.md)
- [Phase 6m-4 Brief](../../uploads/Phase%206m-4%20Brief.md)
- [Production Site](https://creative-pingu.github.io/NutriTrack/)
- [Test Harness](https://creative-pingu.github.io/Nutritrack-proxy/)
- [Main Repository](https://github.com/Creative-pingu/NutriTrack)

---

Document History:
- 2026-08-14: Initial charter created
- Next review: After Phase 6m validation

This document is a living document and should be updated as the project evolves.