# Phase 6n — Multi-Select Food Logging

> **Status**: Queued
> **Owner**: Architect Chat
> **Next Action**: Send brief to dev chat after 6m validates

---

## Overview
Enable logging multiple foods in a single operation, streamlining the workflow for meals with multiple components.

## Brief Status
**Brief Written**: Yes (currently named Phase_6m_Brief_MultiSelect.md)
**Action Needed**: Rename brief to Phase_6n_Brief_MultiSelect.md when sending to dev chat.

## Features

### 1. Batch Meal Selection
- One meal picker for the entire batch
- Select multiple foods before entering quantities

### 2. Per-Food Quantity Entry
- Enter amount for each selected food
- Last-logged amounts pre-filled for each food

### 3. Multi-Select Mode Toggle
- Explicit toggle to enter multi-select mode
- Clear visual indication when in multi-select mode
- Easy to switch between single and multi-select

## User Flow
```
User taps Log Food
    │
    ▼
┌─────────────────────────┐
│  Toggle: Multi-Select    │
│  [OFF] [ON]             │
└─────────────────────────┘
    │
    ▼
┌─────────────────────────┐
│  Food Search             │
│  ┌─────────────────────┐
│  │ Apple               │
│  │ Banana              │
│  └─────────────────────┘
└─────────────────────────┘
    │
    ▼
┌─────────────────────────┐
│  Selected Foods          │
│  ┌─────────────────────┐
│  │ ☑ Apple          [200g] │
│  │ ☑ Banana         [150g] │
│  │ ☐ Rice           [___g] │
│  └─────────────────────┘
│  [+ Add More Foods]      │
│  [Meal: Breakfast ▼]     │
│  [Log All]               │
└─────────────────────────┘
```

## Technical Considerations

### Data Structure
- Multiple log entries created (one per food)
- All entries share the same meal and timestamp
- Each entry has its own snapshot (Phase 5.8 compatibility)

### UI/UX
- Clear visual distinction between modes
- Intuitive selection mechanism
- Efficient quantity entry
- Easy to remove foods from batch

### Performance
- Should handle 10+ foods in a batch without lag
- Search should remain fast with multi-select active

## Dependencies
- Requires Phase 6m completion
- No new runtime dependencies

## References
- [Project Charter](../../PROJECT_CHARTER.md)
- [Development Process](../../DEVELOPMENT_PROCESS.md)