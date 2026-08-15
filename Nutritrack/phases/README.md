# Phase Documentation

This folder contains documentation for each phase of NutriTrack development.

## Folder Structure
```
phases/
├── README.md
├── phase-6m/
│   └── README.md
├── phase-6n/
│   └── README.md
└── ...
```

## Current Phases

| Phase | Status | Folder | Next Action |
|-------|--------|--------|-------------|
| 6m | In Progress | [phase-6m](./phase-6m/) | Awaiting 6m-4 validation |
| 6n | Queued | [phase-6n](./phase-6n/) | Ready after 6m |
| 6o | Queued | (not yet created) | Needs brief |
| 6p | Queued | (not yet created) | After 6n, 6o |

## Phase 6m: SW Update Banner Fix
**Status**: In Progress
**Iterations**: 6m-1 (deployed), 6m-2 (diagnostic), 6m-3 (deployed), 6m-4 (awaiting validation)
**Current**: CACHE_VERSION = nutritrack-v41, v42 pending 6m-4 deploy
**Next**: Validate 6m-4 end-to-end on device

## Phase 6n: Multi-Select Food Logging
**Status**: Queued
**Brief**: Written (Phase_6m_Brief_MultiSelect.md - rename to 6n)
**Features**: One meal picker, per-food quantity entry, multi-select toggle
**Ready**: Immediately after 6m validates

## Phase 6o: Custom Food Manual Promotion
**Status**: Queued - Not yet briefed
**Blocking**: Nick needs to decide on subtype fields (null placeholders vs manual fill)
**Action**: Answer Q5 in FEATURES_AND_IMPROVEMENTS.md

## References
- [Project Charter](../PROJECT_CHARTER.md)
- [Development Process](../DEVELOPMENT_PROCESS.md)
- [Dev Plan v6](../../uploads/Dev%20Plan%20v6.md)