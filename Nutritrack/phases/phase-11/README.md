# Phase 11 - Nutrition Tracking Enhancements

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Depends on**: Phase 10 completion
> **ETC**: 6-8 hours
> **Features**: F3, F4, F5, F6, F13, F14, F22

---

## Problem Statement
Current nutrition tracking lacks visual feedback on goal progress and does not support water or alcohol tracking. Users need clearer indicators of healthy vs unhealthy nutrient intake.

## Requirements

### Core Features
- [ ] Colour coding when exceeding recommended daily intake
- [ ] Info button explaining why exceeding limits is harmful
- [ ] Nutrition options: Recommended (WHO base levels)
- [ ] Nutrition options: Optimal (personalized by activity level)
- [ ] Track water consumption
- [ ] Traffic light system for healthy/not healthy nutrients
- [ ] Alcohol tracking with adjustable percentages

### Traffic Light System
Implement a 3-tier visual system for nutrient intake:

| Tier | Color | Meaning | Action |
|------|-------|---------|--------|
| Green | #10B981 | Below recommended | Encourage more |
| Yellow | #F59E0B | Within recommended range | On track |
| Red | #EF4444 | Above recommended | Warning |

**Application**:
- Apply to all nutrient displays (macros, micros, calories)
- Color applies to both numbers and progress bars
- Hover/tooltip shows recommended range

### Water Tracking
- [ ] Add water consumption logging
- [ ] Track daily water intake
- [ ] Set water goal (configurable, default 2L)
- [ ] Display water progress separately from food
- [ ] Include water in daily summary

### Alcohol Tracking
- [ ] Add alcohol as a category
- [ ] Track alcohol consumption by drink type
- [ ] Adjustable percentage per drink type (beer default 5%, wine 12%, spirits 40%)
- [ ] Calculate alcohol calories (7 kcal/g pure alcohol)
- [ ] Display alcohol units and calories
- [ ] Contribute to daily calorie total

### Nutrition Options
- [ ] Toggle between Recommended and Optimal views
- [ ] Recommended: WHO base levels for general population
- [ ] Optimal: Personalized based on user profile (age, sex, weight, activity)
- [ ] Show both side-by-side or as comparison

### Info Tooltips
- [ ] Info button (i) next to each nutrient
- [ ] Explains:
  - What the nutrient is
  - Why it matters
  - Why exceeding limits is harmful
  - Recommended daily intake
- [ ] Contextual based on nutrient type

## Acceptance Criteria
- [ ] Traffic light colors apply correctly to all nutrients
- [ ] Info tooltips display on tap/click
- [ ] Water consumption can be logged and tracked
- [ ] Alcohol consumption can be logged with adjustable percentages
- [ ] Nutrition options toggle between Recommended and Optimal
- [ ] All changes persist across app restarts
- [ ] Visual feedback is clear and intuitive

## Technical Notes
- No new runtime dependencies
- Storage: localStorage (existing profile and logs)
- Color scheme: Use existing Tailwind colors (green-500, amber-500, red-500)
- Alcohol calculations: 7 kcal per gram of pure alcohol
- Water goal: Configurable in Settings, default 2000ml
- Tooltips: Use existing tooltip component or native title attribute

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Exceed calorie limit | Display turns red |
| V2 | Within protein range | Display turns yellow/green |
| V3 | Below fibre target | Display turns green |
| V4 | Tap info button | Tooltip with explanation appears |
| V5 | Log water consumption | Water progress updates |
| V6 | Log alcohol (beer) | Alcohol calories added to total |
| V7 | Change alcohol percentage | New percentage saved and applied |
| V8 | Toggle nutrition view | Switches between Recommended and Optimal |
| V9 | Check all nutrients | All have traffic light colors |
| V10 | Device validation | All tests pass on iPhone 16e |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../FEATURES_AND_IMPROVEMENTS.md) (F3, F4, F5, F6, F13, F14, F22)
- [USER_GUIDE.md](../USER_GUIDE.md)
- [Phase 10](../phase-10/README.md) (Preceding phase)