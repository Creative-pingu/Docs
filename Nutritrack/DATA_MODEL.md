# NutriTrack — Data Model (Post-5.8)

> **Last Updated**: 2026-08-18
> **Status**: Current

---

## Storage Keys

```javascript
const STORAGE_KEYS = [
  'nt-logs', 'nt-goals', 'nt-custom', 'nt-profile',
  'nt-exratio', 'nt-recipes', 'nt-recents',
  'nt-display-mode', 'nt-energy-unit'
];
// Excluded: nt-logs-backup (manual backup)
```

## Data Types

### FoodItem (830 records from foods.json)
- All nutrient values per 100g
- Includes: macros, fibre subtypes, fat subtypes, 9 amino acids, micronutrients
- Source: USDA (424) + TACO (406) records

### CustomFoodItem
- User-created foods
- Lacks subtype fields (fibre subtypes, fat subtypes, amino acids)
- Soft-delete flag (Phase 5.7)
- Editing safe post-5.8 (snapshot protects history)

### Recipe
- Ingredients with foodId, foodName, amount_g
- nutrition_per_serving computed at creation
- Can be edited without affecting historical logs

### DailyLog Entry Types
#### Food Entry (type: food)
- Includes snapshot of nutrient values at log time (Phase 5.8)
- Read path: snapshot-preferred, foodId-fallback for pre-5.8
- Edit path: Does NOT refresh snapshot

#### Recipe Entry (type: recipe)
- derivedIngredients: quantity snapshot (Phase 3)
- Each ingredient has nutrient-value snapshot (Phase 5.8)
- Editing recipe later does NOT change historical logs

#### Exercise Entry (type: exercise)
- No snapshot needed (no food reference)
- MET-based calorie estimates
- Affects daily goals via refuel ratio

#### Supplement Entry (type: supplement)
- Snapshot of AGGREGATE as-consumed nutrient totals
- NOT per-100g (supplements are dosed items, not weighed sources)
- Flat object of aggregated nutrient values

## Snapshot Principles
- **Quantity Snapshot (Phase 3)**: derivedIngredients freezes ingredient quantities
- **Nutrient-Value Snapshot (Phase 5.8)**: Freezes food nutrient values at log time
- **Supplement Deviation**: Aggregate as-consumed totals, not per-100g
- **Read Path**: Snapshot-preferred, fallback to live foodId for pre-5.8
- **Edit Path**: Does NOT refresh snapshot (historical entries remain historical)
## Phase 11: Water & Alcohol Integration
- **New Fields**: `water` (g/100g), `alcohol` (g/100g) added to every FoodItem
- **Alcohol**: Treated as a first-class macro; calories computed as `alcohol_g × 7` and added to daily total. Drink records have alcohol values set and their `calories` field reduced to food-only.
- **Water**: Currently **`null` (unknown)** for all foods except the explicit "Water" entry. Water content values need to be verified and populated in a future stage. The app treats `null` as 0 in totals/export.
- **UI**: Alcohol appears in the macro ring grid; Water has a dedicated meter (ring + goal) in the log view.
- **Export**: Includes `alcohol_g` and `water_ml` columns.
