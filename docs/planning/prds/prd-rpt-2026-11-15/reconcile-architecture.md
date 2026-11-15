# Input reconciliation — master architecture v0.2

Input: [docs/planning/architecture.md](../../architecture.md), used for constraints only.

| Architecture item | Module PRD |
|---|---|
| §9.2 report and export endpoints (`periodId`, `activityType`, `part=lines|summary`) | RPT.FR-1, RPT.FR-4, RPT.FR-5 |
| §11.2 ReportService → EmissionsQueryService (aggregates) and ActivityService (line items) | §1, RPT.FR-2, RPT.FR-3 |
| ADR-09 decimals as JSON numbers | Addendum: applies to JSON only, not CSV |

**Gaps:**
1. §9.2 has **one** report endpoint that returns the summary and the line items together. The draft's RPT.FR-6 said they "load independently", which that endpoint cannot do, and the claim was not tagged as an assumption. *Fixed:* removed from RPT.FR-6; the choice (one call, or separate summary and line-item calls) is noted for the architect.
2. The M3 summary and monthly queries take a period but no activity type, while RPT.FR-1 filters the summary by type. `EmissionsQueryService` needs an optional activity-type parameter. *Added* to the addendum.
