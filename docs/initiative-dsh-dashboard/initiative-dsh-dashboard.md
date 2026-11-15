---
tracker_id: ""
type: initiative
title: "Dashboard: the period's footprint is understood in ten seconds"
parent: none
sources:
  - docs/initiative-dsh-dashboard/prd-dsh-2026-11-15/prd-dsh-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-dsh-dashboard/prd-dsh-2026-11-15/addendum.md
after: []
risk: low
---

# Dashboard: the period's footprint is understood in ten seconds

## Description

Summary cards, a monthly trend and a breakdown for the selected period, all from M3's aggregates, with text equivalents for every chart. Its empty state opens M2's add-activity form. Child 5 of the parent initiative `initiative-carbontrack`; its requirements come from the M4 module PRD, with the moves recorded in Notes.

## Outcome

An executive reviewer understands the period's total, its sources and its trend at a glance.

## Requirements

- R1 (DSH.FR-1, MVP; parent R37): Period selector
- R2 (DSH.FR-2, MVP; parent R34, R31): Summary cards, with the demonstration disclaimer
- R3 (DSH.FR-3, MVP; parent R35): Monthly trend chart, missing months as zero
- R4 (DSH.FR-4, MVP; parent R36): Breakdown chart
- R5 (DSH.FR-5, MVP; parent R38): Page states, using M0's shared components
- R6 (DSH.FR-6, MVP; parent parent Constraints NFR-04): Text equivalent for every chart
- R7 (DSH.FR-7, Should; parent R39): Interactive charts
- R8 (DSH.FR-8, Could; parent R40): Previous-period comparison

Parent requirements this child delivers all or part of: R31, R34, R35, R36, R37, R38, R39, R40.

## Constraints

- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).
- Module PRD §0 names the master NFRs that matter most here.

## Done when

- SM-1: Ten-second read.: Total, shares and trend are visible without scrolling on a 1366 px wide screen.
- SM-2: One source of numbers.: The dashboard total equals the organization's headline total (ORG.FR-5) and the report summary total (FR-RPT-01) for the same period and data.
- SM-3: Fast enough.: The dashboard's data loads within NFR-02's limit with the 50,000-row dataset.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 2 for the organization's periods (parent tickets.toml).
- Waits on child 3 for totals and monthly aggregates (parent tickets.toml).
- Waits on child 4 for the add-activity form the empty state opens (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R34–R40
- prd — docs/initiative-dsh-dashboard/prd-dsh-2026-11-15/prd-dsh-2026-11-15.md, §4 Features
- addendum — docs/initiative-dsh-dashboard/prd-dsh-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
