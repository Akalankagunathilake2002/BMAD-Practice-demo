# Input reconciliation — M0 to M4 module PRDs

| Module PRD item | Module PRD |
|---|---|
| EMI.FR-4 source label always contains the disclaimer | RPT.FR-4 |
| EMI.FR-5 all three types returned; EMI.FR-6 period required | RPT.FR-2 |
| DSH.FR-1 shared selected period; DSH.FR-3 every month of the period, zeros included | RPT.FR-1, RPT.FR-2 |
| ACT.FR-1 add form; ACT.FR-6 corrections on the Activities page | RPT.FR-3, RPT.FR-6 |
| ORG.FR-4 period order; ORG.FR-7 adding a period | RPT.FR-1, RPT.FR-6 |
| PLT.FR-2 switching resets the period; PLT.FR-13, PLT.FR-14 errors and page states | RPT.FR-1, RPT.FR-6 |

**Gaps:**
1. Like DSH.FR-3, "every month of the period" includes future months of a period that has not ended. For the current year the summary and the summary CSV will contain future months with 0. Consistent with M4 and accepted; *noted* in RPT.FR-2.
2. PLT.FR-2 resets only the period on an organization switch. The draft also cleared the type filter, which nothing requires. *Fixed:* the type filter is kept.
