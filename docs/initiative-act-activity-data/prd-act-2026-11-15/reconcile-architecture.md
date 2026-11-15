# Input reconciliation — master architecture v0.2

Input: [docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md](../../initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md), used for constraints only.

| Architecture item | Module PRD |
|---|---|
| §5 request flow: create and calculate in one transaction | ACT.FR-1 |
| §8.2 activity_records constraints (quantity > 0, type/unit pair, description 500) | ACT.FR-2 |
| §9.1 pagination and sort conventions | ACT.FR-4 |
| §9.2 activity endpoints, detail with calculation history | ACT.FR-4, ACT.FR-5, ACT.FR-6, ACT.FR-8 |
| §10.2 activities route with add and edit dialogs | ACT.FR-1 |
| §12.3 validation in three layers | ACT.FR-2 |

**Gaps:**
1. `quantity` is `NUMERIC(18,3)`, which allows up to 15 integer digits, while FR-ACT-02 caps it at 1,000,000,000. The service enforces the cap; no change. Noted.
2. Sorting by emissions needs a join to the current calculation (addendum).
3. The error code for a date outside every period, `ACTIVITY_DATE_OUTSIDE_PERIODS`, already exists in §6.3. ACT.FR-2 matches it.
