# Input reconciliation — master architecture v0.2

Input: [docs/planning/architecture.md](../../architecture.md), used for constraints only.

| Architecture item | Module PRD |
|---|---|
| §5 request flow, §6.2 one transaction per request | EMI.FR-1 (save and calculation together), EMI.FR-9 (all-or-nothing) |
| §7 factor lifecycle, recalculation, SQL aggregation | EMI.FR-7, EMI.FR-9, EMI.FR-6 |
| §8.2 `emission_factors.value` NUMERIC(12,6), CHECK ≥ 0 | EMI.FR-7 value rules |
| §9.2 M3 endpoints | §7 interfaces; addendum architecture impacts 1–3 |
| §10.2 `/emission-factors` route, §11.1 EmissionFactorsPage | EMI.FR-8, Q-18 |

**Gaps:**
1. `emission_factors` has no `created_at` column: only mutable tables get timestamps (§8.2), and factor rows are immutable. EMI.FR-8 needs the date each version was added. *Fixed:* added as architecture impact 5 in the addendum and to open question 1.
2. The upper bound on a factor value follows from NUMERIC(12,6) but was not in EMI.FR-7. *Fixed* in EMI.FR-7.
