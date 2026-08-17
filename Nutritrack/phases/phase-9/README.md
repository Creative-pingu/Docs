# Phase 9 - Custom Food Promotion and Deployment

> **Status**: Complete
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Combines**: Phase 6o (Custom Food) + Phase 10 (Deployment)
> **Depends on**: Phase 8 completion
> **ETC**: 5-7 hours
> **Shipped**: App v67 (CACHE_VERSION nutritrack-v67); PR [#12](https://github.com/Creative-pingu/NutriTrack/pull/12)
> **Validated**: Deployed to GitHub Pages (build `built` for commit f8fd0f6)

---

## Overview
This phase combines two related feature sets: Custom Food Manual Promotion and Deployment Infrastructure improvements.

## Part 1: Custom Food Manual Promotion (from Phase 6o)

### Problem Statement
Custom foods currently lack subtype fields (fibre subtypes, fat subtypes, amino acids). When exporting custom foods as JSON patches, the schema is inconsistent with the main food database.

### Decision (A8)
Use null placeholders for all missing subtype fields in custom food JSON patches to maintain schema consistency with the main foods.json structure.

### Requirements
- [x] Custom food creation captures all available fields
- [x] JSON patch export includes ALL schema fields
- [x] Missing fields explicitly set to `null` (not omitted)
- [x] Patch can be applied cleanly to foods.json
- [x] Existing custom foods can be migrated to new format

### Data Structure
All 33 nutrient fields must be present in export (value or null):
- Macros: calories, protein, carbohydrates, fat, fibre
- Fibre subtypes: fibre_soluble, fibre_insoluble
- Fat subtypes: fat_saturated, fat_mufa, fat_pufa
- Amino acids: histidine, isoleucine, leucine, lysine, methionine, phenylalanine, threonine, tryptophan, valine
- Micronutrients: iron, calcium, zinc, b12, vitamin_d, omega3, iodine, selenium, magnesium, potassium, folate, sodium, vitamin_a, vitamin_c

### Acceptance Criteria
- [x] Custom food export produces valid JSON matching foods.json schema v1
- [x] All 33 nutrient fields present in export (value or null)
- [x] Patch can be manually applied to foods.json without errors
- [x] Existing custom foods can be migrated to new format

---

## Part 2: Deployment and Infrastructure (from Phase 10)

### Problem Statement
Current deployment uses GitHub Pages with basic versioning. Need to review and improve deployment topology and versioning strategy.

### Requirements
- [x] Review GitHub Pages as deployment platform
- [x] Evaluate alternative deployment options if needed
- [x] Improve CACHE_VERSION strategy in sw.js
- [x] Mitigate GitHub Pages downtime impact
- [x] Handle SW update failures gracefully

### Acceptance Criteria
- [x] Deployment platform decision documented
- [x] Versioning strategy improved and documented
- [x] Downtime mitigation plan in place
- [x] SW update failure handling implemented

---

## Technical Notes
- No new runtime dependencies
- Schema version: 1 (match foods.json)
- Storage: localStorage (existing custom foods)
- Migration: One-time conversion for existing custom foods
- Deployment: GitHub Pages (current, open to reconsideration)

## Implementation Summary (as shipped, v67)

### Part 1 — Custom Food Manual Promotion (A8)
- **Schema-complete custom foods.** Internal abbreviation-keyed custom foods (`cal`, `pro`, ...) are promoted to the foods.json schema v1 long-name shape (`calories`, `protein`, ...) via `customFoodToDbRecord()`. **All 33 nutrient fields are present** (value or `null`); the 14 subtype keys (fibre/fat subtypes + amino acids) the form does not collect are explicitly `null`, matching foods.json convention.
- **JSON patch export.** `buildCustomFoodPatch()` emits an RFC 6902 `add`-ops envelope (`schema_version: 1`, `basis: per_100g`) that appends cleanly to the `foods` array; soft-deleted foods are excluded. An **Export patch** button in *Manage Custom Foods* downloads the patch as JSON and confirms.
- **One-time migration.** `migrateCustomFoods()` backfills all numeric nutrient keys to `null` for legacy (pre-Phase-9) records; wired into the load effect (idempotent, persists). Existing custom foods display correctly after migration.
- **Edit custom foods.** `openEditCustomFood()` loads an existing food into the form (preserving saved subtype values + the soft-delete flag); `saveCustomFood()` updates in-place when editing. An **Edit** button sits beside Delete on each active food.
- **Simple/Advanced form toggle.** The custom-food form defaults to **Simple** (6 standard package values: Calories, Protein, Carbs, Fat, Fibre, Sodium); **Advanced** surfaces all 19 NUTRIENT_META fields. Entering via "+ Custom"/"+ New" resets to create-mode; editing opens Advanced.
- **Settings entry.** A new **Custom Foods** card in Settings (above Export Data) shows the active count and a "Manage custom foods" button.
- **Bug fix.** `saveCustomFood`/`setCf` reset previously dropped `sod`/`vitA`/`vitC`; fixed.

### Part 2 — Deployment & Infrastructure (R4/R10)
- **SW update-failure handling.** The update banner now handles failures gracefully: if there is no waiting worker / no controller / SW is unsupported, it hard-reloads; otherwise it sends `SKIP_WAITING` with a 4s `controllerchange` timeout fallback so the banner never strands the user on a stale build.
- **Versioning.** `CACHE_VERSION` (sw.js) and `SHELL_APP_VERSION` (index.html) are bumped together and kept in sync with the inline comment and `build-info`. Phase 9 shipped v65 → v66 → v67.
- **Deployment platform.** GitHub Pages retained (network-first index.html + cache fallback makes the PWA work offline; see V6).
- **Build artifact.** `NutriTrack.js` rebuilt from `NutriTrack.jsx` (Babel preset-react classic runtime) and committed.

## Follow-up (deferred to a later phase)

### Custom-food export → direct merge into foods.json

Phase 9 ships custom-food export as a **downloadable JSON patch** (RFC 6902 `add` ops, foods.json schema v1) that a maintainer appends to the `foods` array by hand. The patch is merge-ready, but it is **not** wired to write directly into the deployed `foods.json` (the PWA has no write path to the repo, and direct in-place merges would risk corrupting the canonical DB).

**Deferred work — picked up in a future phase:**
- Split the monolithic `foods.json` into **separate per-category / per-region files** (see Phase 13 — Database Architecture) so promoted custom foods can merge into a smaller, scoped file.
- Provide a merge tooling path (likely a build-time script in the repo, not a runtime PWA write) that consumes the exported patch and writes the merged files, with validation against schema v1.
- Surface a one-tap "promote" action in the app once the merge tooling is available (the export button + patch format landed in Phase 9 are the foundation for this).

No code change is required for Phase 9 sign-off; the exported patch already merges cleanly (verified against the real 830-food foods.json).

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
| **E1** | Edit a custom food (Manage → Edit) | Updates in place, no duplicate; preserves subtypes/flags |
| **E2** | Edit preserves subtypes + deleted flag | Round-trip keeps saved subtypes; deleted stays deleted |
| **E3** | Simple/Advanced toggle (new food) | Simple = 6 fields (default); Advanced = 19 fields; saves usable food |
| **E4** | Settings → Custom Foods card | Card present above Export Data; opens Manage Custom Foods |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../FEATURES_AND_IMPROVEMENTS.md) (A3, A4, A5, A8)
- [RISK_REGISTER.md](../RISK_REGISTER.md) (R4, R10)
- [Phase 8](../phase-8/README.md) (Preceding phase)