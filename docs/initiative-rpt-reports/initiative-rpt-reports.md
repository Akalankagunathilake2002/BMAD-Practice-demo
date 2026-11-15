---
tracker_id: ""
type: initiative
title: "Reports: exported CSVs add up exactly to the totals on screen"
parent: none
sources:
  - docs/initiative-rpt-reports/prd-rpt-2026-11-15/prd-rpt-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-rpt-reports/prd-rpt-2026-11-15/prd-rpt-2026-11-15.md
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-rpt-reports/prd-rpt-2026-11-15/addendum.md
after: []
risk: low
---

# Reports: exported CSVs add up exactly to the totals on screen

## Description

A period report with summary and line items, and full-precision CSV exports that carry the demonstration disclaimer, from M3's aggregates and M2's activity records. Child 6 of the parent initiative `initiative-carbontrack`; its requirements come from the M5 module PRD, with the moves recorded in Notes.

## Outcome

A finance colleague opens the exported CSVs, sums them and gets exactly the totals on screen.

## Requirements

- R1 (RPT.FR-1; parent R42): A report needs a reporting period, defaulting to the period selected on the dashboard, and takes an optional activity type that filters the summary, the line items and both exports and is named on the page and in file names; both filters live in the page address, and switching organization resets only the period.
- R2 (RPT.FR-2; parent R41): The summary shows the total, each type with its share (all three without a type filter, 0 when empty) and every month of the period with each type, zero for months without activity, in kg CO2e to 2 decimals, as tables only, matching the dashboard total.
- R3 (RPT.FR-3; parent R43, R25, R31): Read-only line items list the matching activities oldest first with date, type, quantity, unit, the factor used with its version, and emissions in kg CO2e to 2 decimals, paginated with the total count, with the disclaimer on the page.
- R4 (RPT.FR-4; parent R44, R31): The line items CSV holds every matching line item under one header row, UTF-8 with a byte-order mark, dates as YYYY-MM-DD, numbers at full stored precision with a dot and no separators, each row's factor value, version and disclaimer-carrying source label, descriptions starting with = + - or @ prefixed with an apostrophe, and a file name naming organization, period, type filter, content and date; its emissions column sums exactly to the report total, and 50,000 rows export within 10 seconds.
- R5 (RPT.FR-5, Should; parent R45): The summary CSV has one header row, one row per month of the period with a column per type and a total column, a final Total row equal to the report total, a source column carrying the disclaimer on every row, and R4's precision.
- R6 (RPT.FR-6; parent R31): Summary and line items show skeletons while loading; with no matching activity the summary shows zeros and the line items an empty state whose "Add activity" opens M2's add form; with no periods the page leads to adding one; exports are disabled with a reason when there are no line items; a failed load or export shows Retry and never leaves a partial file.
- R7 (RPT.FR-7, Could; parent R33): Line items, the type breakdown and the line items CSV carry the activity type's informational scope label.

## Constraints

- The report calculates nothing: figures come from M3 and line items from M2's records, so it always matches the dashboard (EMI.FR-5, EMI.FR-6).
- CSV numbers keep full stored precision so spreadsheet sums equal the totals; the screen shows 2 decimals (NFR-01).
- The demonstration disclaimer travels with every exported file (FR-EMI-09, Q-10).
- Text written to a CSV is protected against spreadsheet formula injection (NFR-06).
- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).

## Done when

- SM-1: Spreadsheet check: For any report, summing the emissions column of the line items CSV gives exactly the report total, and the summary CSV's Total row matches it.
- SM-2: One source of numbers: The report total equals the dashboard total for the same period and type (DSH.FR-2).
- SM-3: Opens cleanly: Both CSV files open in Microsoft Excel, LibreOffice Calc and Google Sheets with the right columns, numbers and characters, without import settings.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 3 for totals and monthly aggregates (parent tickets.toml).
- Waits on child 4 for activity records for line items (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R41–R45
- prd — docs/initiative-rpt-reports/prd-rpt-2026-11-15/prd-rpt-2026-11-15.md, §4 Features (adopted companion: each FR's testable consequences)
- addendum — docs/initiative-rpt-reports/prd-rpt-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
