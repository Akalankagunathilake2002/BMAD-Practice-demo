# Input reconciliation — master architecture v0.2

Input: [docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md](../../initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md), used for constraints only.

| Architecture item | Module PRD |
|---|---|
| §8.2 organizations: unique `lower(name)` | ORG.FR-1, ORG.FR-6 |
| §8.2 reporting_periods: name length 50, end ≥ start, no-overlap exclusion constraint | ORG.FR-7 |
| §9.2 organization and period endpoints | ORG.FR-1, ORG.FR-3 to ORG.FR-8 |
| §10.2 routes `/organizations`, `/organizations/new`, `/organizations/:orgId/settings` | ORG.FR-1, ORG.FR-3, ORG.FR-4 |

**Gaps:**
1. ORG.FR-7 wants the overlap error to name the overlapping period. The database exclusion constraint refuses overlaps but cannot say which period clashed, so the service must look it up first. *Added* to the addendum's architecture notes.
2. Period names that are unique ignoring case need an index change (already in the addendum).
