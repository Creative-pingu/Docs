# Phase 10 - Recipe Management

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Depends on**: Phase 9 completion
> **ETC**: 4-6 hours
> **Features**: F1, F2

---

## Problem Statement
Currently, recipes are static after creation. Users need to edit recipes to remove unused ingredients or update quantities, which would streamline the logging workflow.

## Requirements

### Core Features
- [ ] Recipes act as templates that can be edited when logged
- [ ] Edit recipes: update ingredients and quantities
- [ ] Remove unused ingredients from recipes

### User Flow
```
1. User navigates to Recipes
2. User selects a recipe
3. User taps Edit
4. User can:
   - Add/remove ingredients
   - Update ingredient quantities
   - Reorder ingredients
5. User saves changes
6. Recipe is updated and can be logged with new values
```

### Data Structure
- Recipe maintains its ID but updates ingredientLines array
- Each recipe has: id, title, servings, source, ingredientLines[]
- ingredientLines: array of strings (e.g., "200g apple", "150g banana")
- Changes persist in localStorage

## Acceptance Criteria
- [ ] User can edit existing recipes
- [ ] User can add ingredients to recipe
- [ ] User can remove ingredients from recipe
- [ ] User can update ingredient quantities
- [ ] Edited recipe can be logged successfully
- [ ] Original recipe data preserved until saved
- [ ] All changes persist across app restarts

## Technical Notes
- No new runtime dependencies
- Storage: localStorage (existing recipes)
- Compatibility: Works with existing recipe logging system
- Validation: Recipe must have at least 1 ingredient

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Open recipe for editing | Edit interface displayed |
| V2 | Add ingredient to recipe | Ingredient appears in list |
| V3 | Remove ingredient from recipe | Ingredient removed from list |
| V4 | Update ingredient quantity | Quantity updated in list |
| V5 | Save edited recipe | Changes persisted |
| V6 | Log edited recipe | Recipe logged with updated ingredients |
| V7 | Cancel edit | Original recipe unchanged |
| V8 | Edit recipe with 1 ingredient | Can still edit (no minimum enforced) |
| V9 | Device validation | All tests pass on iPhone 16e |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../FEATURES_AND_IMPROVEMENTS.md) (F1, F2)
- [USER_GUIDE.md](../USER_GUIDE.md)
- [Phase 9](../phase-9/README.md) (Preceding phase)