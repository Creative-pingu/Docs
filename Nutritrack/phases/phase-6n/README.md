# Phase 6n - Multi-Select Food Logging

> **Status**: Ready for Implementation
> **Owner**: Dev Chat
> **Architect**: Architect Chat
> **Depends on**: Phase 6m completion

---

## Problem Statement
Logging meals with multiple foods requires individual entries. Batch logging would streamline this common workflow.

## Requirements

### Core Features
- One meal picker for entire batch
- Per-food quantity entry with last-logged pre-fill
- Explicit toggle to enter multi-select mode

### User Flow
1. User taps Log Food
2. User toggles Multi-Select mode ON
3. User searches and selects multiple foods
4. For each food: enter quantity (pre-filled with last-logged amount)
5. Select meal type (applies to all foods in batch)
6. Tap Log All to create individual entries

### Data Structure
- Creates multiple log entries (one per food)
- All entries share same meal type and timestamp
- Each entry has its own snapshot (Phase 5.8 compatibility)

## Acceptance Criteria
- Multi-select toggle clearly visible and intuitive
- Selected foods displayed in list with quantities
- Last-logged amounts pre-filled for each food
- Can add/remove foods from batch
- Single Log All action creates all entries atomically
- Works with 10+ foods without lag
- Search remains fast with multi-select active

## UI/UX Requirements
- Clear visual distinction between single and multi-select modes
- Intuitive selection mechanism (checkboxes or similar)
- Efficient quantity entry (number input with grams)
- Easy to remove foods from batch
- Meal picker: one selection applies to all

## Technical Notes
- No new runtime dependencies
- Storage: existing log entries in localStorage
- Performance: handle 10+ foods efficiently
- Compatibility: works with existing Phase 5.8 snapshot system

## Validation Matrix
| Test | Action | Expected Result |
|------|--------|-----------------|
| V1 | Toggle multi-select ON | Mode indicator visible |
| V2 | Search and select 3 foods | All 3 appear in selected list |
| V3 | Enter quantities for all | Quantities saved per food |
| V4 | Select meal type | Applies to all foods |
| V5 | Tap Log All | 3 separate entries created |
| V6 | Verify entries | Each has correct food, quantity, meal, timestamp |
| V7 | Toggle multi-select OFF | Returns to single mode |
| V8 | Select 10+ foods | No performance lag |

## References
- [FEATURES_AND_IMPROVEMENTS.md](../../FEATURES_AND_IMPROVEMENTS.md)
- [USER_GUIDE.md](../../USER_GUIDE.md)
- [Phase 6m](../phase-6m/README.md)