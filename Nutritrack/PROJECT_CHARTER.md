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
- Budget: No recurring costs; one-off purchases <€5 acceptable

### Constraints

- **Budget**: No recurring costs; one-off purchases <€5 acceptable
- **Dependencies**: No new runtime dependencies without architect approval
- **Deployment**: GitHub Pages (current choice, open to reconsideration)
- **Testing**: Must pass device validation on iPhone 16e
- **Browser Support**: Primary focus on iPhone 16e Safari, but support other browsers
