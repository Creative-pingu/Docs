# Architecture Decision Records (ADRs)

This folder contains Architecture Decision Records for NutriTrack.

## What is an ADR?
An Architecture Decision Record captures an important architectural decision with its context and consequences.

## ADR Template
```markdown
# ADR-[NNN] - [Decision Title]

## Status
- Accepted
- Rejected
- Superseded by [ADR-XXX]

## Context
[The problem or opportunity]

## Decision
[The chosen solution]

## Consequences
[Positive and negative outcomes]

## Alternatives Considered
[Other options evaluated]
```

## Existing ADRs
| Number | Title | Status | Date |
|--------|-------|--------|------|
| [ADR-001](./ADR-001-localStorage.md) | Use localStorage for Data Storage | Accepted | 2026-08-14 |
| [ADR-002](./ADR-002-no-backend.md) | No Backend Principle | Accepted | 2026-08-14 |

## How to Add a New ADR
1. Create file: ADR-[NNN]-[short-name].md
2. Use the template above
3. Add to the table
4. Commit to repository

## ADR Process
1. Propose: Anyone can propose an ADR
2. Discuss: Architect chat reviews
3. Decide: Architect chat makes final decision
4. Document: Create ADR in this folder
5. Review: Review chat verifies technical soundness
6. Implement: Dev chat implements the decision

## Notes
- ADRs are immutable once accepted
- ADRs can be superseded by newer decisions
- ADRs should be concise and focused on one decision