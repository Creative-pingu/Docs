# Phase 6o - Custom Food Manual Promotion

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Decision**: A8 resolved - Use null placeholders for missing subtype fields
> **Depends on**: Phase 6m completion

---

## Problem Statement
Custom foods currently lack subtype fields (fibre subtypes, fat subtypes, amino acids). When exporting custom foods as JSON patches, the schema is inconsistent with the main food database.

## Decision (A8)
Use null placeholders for all missing subtype fields in custom food JSON patches to maintain schema consistency with the main foods.json structure.

## Requirements

### Functional
- Custom food creation captures all available fields
- JSON patch export includes ALL schema fields
- Missing fields explicitly set to null (not omitted)
- Patch can be applied cleanly to foods.json

### Data Structure
All 25+ nutrient fields must be present in export (value or null):
- Macros: calories, protein, carbohydrates, fat, fibre
- Fibre subtypes: fibre_soluble, fibre_insoluble
- Fat subtypes: fat_saturated, fat_mufa, fat_pufa
- Amino acids: histidine, isoleucine, leucine, lysine, methionine, phenylalanine, threonine, tryptophan, valine
- Micronutrients: iron, calcium, zinc, b12, vitamin_d, omega3, iodine, selenium, magnesium, potassium, folate, sodium, vitamin_a, vitamin_c

## Acceptance Criteria
- Custom food export produces valid JSON matching foods.json schema v1
- All 25+ nutrient fields present in export (value or null)
- Patch can be manually applied to foods.json without errors
- Existing custom foods can be migrated to new format

## Technical Notes
- Schema version: 1 (match foods.json)
- No new runtime dependencies
- Storage: localStorage (existing custom foods)
- Migration: One-time conversion for existing custom foods

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Create custom food with minimal fields | Export includes all fields with nulls |
| V2 | Create custom food with some subtype fields | Export preserves values, nulls for missing |
| V3 | Apply patch to foods.json | Patch merges cleanly, no schema errors |
| V4 | Load app after migration | Custom foods display correctly |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../../FEATURES_AND_IMPROVEMENTS.md) (A8)
- [foods.json Schema](../../../NutriTrack/foods.json)
- [Phase 6m](../phase-6m/README.md)