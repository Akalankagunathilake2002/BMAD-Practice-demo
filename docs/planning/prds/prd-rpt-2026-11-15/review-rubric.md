# PRD Quality Review — Module PRD: Reports (M5)

Reviewed: prd.md and addendum.md, draft of 2026-11-15. Stakes: internal demo, module PRD feeding UX (CP4) and stories (CP5).

## Overall verdict

**Good.** The PRD has a sharp, testable thesis (the spreadsheet sum equals the screen total) and SM-1 checks it directly. The user's four decisions are reflected exactly, and the disclaimer gap in the summary CSV was caught and resolved. There are no critical or high findings. Two medium findings (an untagged claim that conflicts with the architecture, and a missing type parameter in M3's queries) and two low ones are fixed.

## Decision-readiness — strong

The four decisions are stated plainly, and the reason for full precision is in the addendum.

## Substance over theater — strong

## Strategic coherence — strong

SM-C1 protects the disclaimer against the natural pull to tidy the CSV.

## Done-ness clarity — adequate

### Findings
- **medium** RPT.FR-6: "the summary and the line items … each loads independently (as on the dashboard)" is untagged and contradicts architecture §9.2, where one endpoint returns both. *Fix:* remove it and leave the endpoint shape to the architect.
- **low** SM-3 "common spreadsheet programs" is vague. *Fix:* name Microsoft Excel, LibreOffice Calc and Google Sheets.
- **low** RPT.FR-1 clears the type filter on an organization switch. Nothing requires this, and the user would lose the filter. *Fix:* keep the type filter; only the period resets (PLT.FR-2).

## Scope honesty — strong

Non-goals cover PDF, multi-period reports, editing and calculation. Ten assumptions, all indexed.

## Downstream usability — adequate

### Findings
- **medium** RPT.FR-1 filters the summary by type, but EMI.FR-5 and EMI.FR-6 (and their endpoints) take no type. *Fix:* addendum note for the architect: `EmissionsQueryService` needs an optional activity type.

## Shape fit — strong

## Mechanical notes

- The assumptions index matches the inline tags (10 = 10).
- RPT.FR-1 to 7 are contiguous; SM-1 to 3 and SM-C1 are present.
