# Phase 9 - Custom Food Promotion and Deployment

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Combines**: Phase 6o (Custom Food) + Phase 10 (Deployment)
> **Depends on**: Phase 8 completion
> **ETC**: 5-7 hours

---

## Overview
This phase combines two related feature sets: Custom Food Manual Promotion and Deployment Infrastructure improvements.

## Part 1: Custom Food Manual Promotion (from Phase 6o)

### Problem Statement
Custom foods currently lack subtype fields (fibre subtypes, fat subtypes, amino acids). When exporting custom foods as JSON patches, the schema is inconsistent with the main food database.

### Decision (A8)
Use null placeholders for all missing subtype fields in custom food JSON patches to maintain schema consistency with the main foods.json structure.

### Requirements
- [ ] Custom food creation captures all available fields
- [ ] JSON patch export includes ALL schema fields
- [ ] Missing fields explicitly set to `null` (not omitted)
- [ ] Patch can be applied cleanly to foods.json
- [ ] Existing custom foods can be migrated to new format

### Data Structure
All 25+ nutrient fields must be present in export (value or null):
- Macros: calories, protein, carbohydrates, fat, fibre
- Fibre subtypes: fibre_soluble, fibre_insoluble
- Fat subtypes: fat_saturated, fat_mufa, fat_pufa
- Amino acids: histidine, isoleucine, leucine, lysine, methionine, phenylalanine, threonine, tryptophan, valine
- Micronutrients: iron, calcium, zinc, b12, vitamin_d, omega3, iodine, selenium, magnesium, potassium, folate, sodium, vitamin_a, vitamin_c

### Acceptance Criteria
- [ ] Custom food export produces valid JSON matching foods.json schema v1
- [ ] All 25+ nutrient fields present in export (value or null)
- [ ] Patch can be manually applied to foods.json without errors
- [ ] Existing custom foods can be migrated to new format

---

## Part 2: Deployment and Infrastructure (from Phase 10)

### Problem Statement
Current deployment uses GitHub Pages with basic versioning. Need to review and improve deployment topology and versioning strategy.

### Requirements
- [ ] Review GitHub Pages as deployment platform
- [ ] Evaluate alternative deployment options if needed
- [ ] Improve CACHE_VERSION strategy in sw.js
- [ ] Mitigate GitHub Pages downtime impact
- [ ] Handle SW update failures gracefully

### Acceptance Criteria
- [ ] Deployment platform decision documented
- [ ] Versioning strategy improved and documented
- [ ] Downtime mitigation plan in place
- [ ] SW update failure handling implemented

---

## Technical Notes
- No new runtime dependencies
- Schema version: 1 (match foods.json)
- Storage: localStorage (existing custom foods)
- Migration: One-time conversion for existing custom foods
- Deployment: GitHub Pages (current, open to reconsideration)

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Create custom food with minimal fields | Export includes all fields with nulls |
| V2 | Create custom food with some subtype fields | Export preserves values, nulls for missing |
| V3 | Apply patch to foods.json | Patch merges cleanly, no schema errors |
| V4 | Load app after migration | Custom foods display correctly |
| V5 | Check deployment versioning | CACHE_VERSION updated correctly |
| V6 | Simulate GitHub Pages downtime | App continues to work offline |
| V7 | Device validation | All tests pass on iPhone 16e |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../FEATURES_AND_IMPROVEMENTS.md) (A3, A4, A5, A8)
- [RISK_REGISTER.md](../RISK_REGISTER.md) (R4, R10)
- [Phase 8](../phase-8/README.md) (Preceding phase)