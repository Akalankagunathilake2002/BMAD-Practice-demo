---
tracker_id: ""
type: initiative
title: "Reports: exported CSVs add up exactly to the totals on screen"
parent: none
sources:
  - docs/initiative-rpt-reports/prd-rpt-2026-11-15/prd-rpt-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-rpt-reports/prd-rpt-2026-11-15/addendum.md
after: []
risk: low
---

# Reports: exported CSVs add up exactly to the totals on screen

## Description

A period report with summary and line items, and full-precision CSV exports that carry the demonstration disclaimer. Child 6 of the parent initiative `initiative-carbontrack`; its requirements come from the M5 module PRD.

## Outcome

A finance colleague opens the exported CSVs, sums them and gets exactly the totals on screen.

## Requirements

- R1 (RPT.FR-1, MVP): Report filters
- R2 (RPT.FR-2, MVP): Period summary
- R3 (RPT.FR-3, MVP): Line items
- R4 (RPT.FR-4, MVP): Line items CSV
- R5 (RPT.FR-5, Should): Summary CSV
- R6 (RPT.FR-6, MVP): Page states
- R7 (RPT.FR-7, Could): Scope label in the report

Parent requirements this child covers: R41, R42, R43, R44, R45.

## Constraints

- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).
- Module PRD §0 names the master NFRs that matter most here.

## Done when

- SM-1: Spreadsheet check.: For any report, summing the emissions column of the line items CSV gives exactly the report total, and the summary CSV's Total row matches it.
- SM-2: One source of numbers.: The report total equals the dashboard total for the same period and type (DSH.FR-2).
- SM-3: Opens cleanly.: Both CSV files open in Microsoft Excel, LibreOffice Calc and Google Sheets with the right columns, numbers and characters, without import settings.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 3 for totals and monthly aggregates (parent tickets.toml).
- Waits on child 4 for activity records for line items (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R41–R45
- prd — docs/initiative-rpt-reports/prd-rpt-2026-11-15/prd-rpt-2026-11-15.md, §4 Features
- addendum — docs/initiative-rpt-reports/prd-rpt-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership
