# Input reconciliation — master architecture v0.2

Input: [docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md](../../initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md), used for constraints only.

| Architecture item | Module PRD |
|---|---|
| §9.2 summary and monthly endpoints | DSH.FR-2 to DSH.FR-4 |
| §10.1 `components/charts/` with accessible table equivalent | DSH.FR-6 |
| §10.1 mutations invalidate related queries | DSH.FR-5 |
| §10.2 period in the page address, shared with Reports | DSH.FR-1 |
| §10.4 one chart colour per activity type | DSH.FR-3 |

**Gaps:**
1. §11.1 assigns `components/charts/` to **M4**. The draft's §7 said the chart components are M0's. *Fixed:* M4 owns them, and M5 can reuse them.
2. "Add activity data" should open the Activities page with the add dialog already open (ACT.FR-1). The route needs a parameter for that. *Added* to the addendum.
