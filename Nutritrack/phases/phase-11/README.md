# Phase 11 - Enhanced Tracking and UX Polish

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Depends on**: Phase 10 completion
> **ETC**: 8-12 hours
> **Structure**: Three sub-parts (11a, 11b, 11c)
> **Priority**: Water/Alcohol > Goal Tracking > Bug Fixes

---

## Overview
This phase combines new tracking features with critical bug fixes and UX improvements based on Phase 10 feedback.

---

## Part 11a: Water and Alcohol Tracking (HIGHEST PRIORITY)

### Problem Statement
Users need to track water consumption and alcohol intake as part of their nutritional goals. Alcohol contributes to calorie intake and should be trackable with adjustable ABV percentages.

### Requirements
- Add water tracking functionality
- Add alcohol tracking with drink categories
- Configurable ABV percentage per drink category (default: beer 5%, wine 12%, spirits 40%)
- Alcohol contributes to calorie calculations
- Water/alcohol data visible in daily log
- Water/alcohol included in export data

### User Flow
1. User taps Log Food
2. User selects Water or Alcohol category
3. For Water: Enter amount (ml, glasses, etc.), select meal type, log entry
4. For Alcohol: Select drink category, enter volume, ABV percentage (pre-filled with category default, editable), calculate calories, select meal type, log entry

### Data Structure
Water: { amount, unit, timestamp, meal }
Alcohol: { category, volume, abv, calories, timestamp, meal }
Calories formula: volume * abv% * 7.89 kcal/g alcohol

### Acceptance Criteria
- User can log water consumption
- User can log alcohol with drink categories
- ABV defaults are correct (beer 5%, wine 12%, spirits 40%)
- ABV is editable per entry
- Alcohol calories calculated correctly
- Water/alcohol appears in daily log
- Water/alcohol data exported in ZIP

---

## Part 11b: Goal Tracking Enhancements

### Problem Statement
Users need better visual feedback on their nutritional intake and more flexible goal options.

### Requirements
- Traffic light system for nutrient display (green/yellow/red)
- Colour coding when exceeding recommended daily intake
- Info button explaining why exceeding limits is harmful
- Nutrition options: Recommended (WHO base levels)
- Nutrition options: Optimal (personalized by activity level)

### Traffic Light System
Green (< 100% of target), Yellow (100-120%), Red (> 120%)
Applies to: Calories, Protein, Carbs, Fat, Fibre, and all micronutrients

### Acceptance Criteria
- Traffic light colors applied to all nutrient displays
- Color thresholds configurable
- Info tooltips explain each nutrient
- WHO recommended levels available as preset
- Personalized optimal levels can be set

---

## Part 11c: Bug Fixes and UX Polish

### Requirements
- B1: After selecting a food, options should reset
- B2: Page zoom/centering issues fixed
- B3: Back button behavior - ingredients list to recipe screen
- B4: Scroll to bottom goes to last entry, not blank screen
- B5: Multi-select with checkboxes per item
- B6: Bigger date change button (min 44x44px)
- B7: Remove source field from recipe tab

### Bug Fix Details
B1: Clear search field after food selection, clear quantity field after logging
B2: Investigate viewport meta tag, check CSS overflow properties, test on iPhone 16e Safari
B3: Back should return to ingredients list, maintain navigation stack properly
B4: Calculate exact position of last entry, use scrollIntoView with proper alignment
B5: Replace toggle mode with per-item checkboxes, selected foods highlighted, bulk actions available
B6: Increase tap target size, add padding, position for thumb reachability
B7: Remove source field from recipe editing UI, keep in data structure for compatibility

### Acceptance Criteria
- Food selection clears search/quantity fields
- Page displays correctly without zoom issues
- Back button navigates to ingredients list
- Scroll to bottom positions correctly
- Multi-select uses checkboxes per item
- Date button is easy to press
- Source field removed from recipe tab

---

## Technical Notes
- No new runtime dependencies
- Water/Alcohol: New data types in localStorage
- Traffic Light: CSS color classes, configurable thresholds
- Bug Fixes: Various UI/UX improvements

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Log water consumption | Water entry appears in daily log |
| V2 | Log alcohol with default ABV | Alcohol entry with correct calories |
| V3 | Edit ABV percentage | Custom ABV saved and used |
| V4 | Exceed calorie target | Nutrient display shows red |
| V5 | Near protein target | Nutrient display shows yellow |
| V6 | Under carb target | Nutrient display shows green |
| V7 | Tap info button | Tooltip with explanation appears |
| V8 | Select food, then log | Search/quantity fields reset |
| V9 | Navigate app | No zoom/centering issues |
| V10 | Add ingredient to recipe | Back returns to ingredients list |
| V11 | Scroll to bottom | Last entry visible, not blank screen |
| V12 | Multi-select mode | Checkboxes visible per item |
| V13 | Change date | Easy to tap, no mis-taps |
| V14 | Edit recipe | Source field not visible |
| V15 | Device validation | All tests pass on iPhone 16e |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../FEATURES_AND_IMPROVEMENTS.md) (F3-F16, B1-B7)
- [USER_GUIDE.md](../USER_GUIDE.md)
- [Phase 10](../phase-10/README.md) (Preceding phase)