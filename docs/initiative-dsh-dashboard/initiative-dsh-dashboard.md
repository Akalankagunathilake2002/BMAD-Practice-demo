---
tracker_id: ""
type: initiative
title: "Dashboard: the period's footprint is understood in ten seconds"
parent: none
sources:
  - docs/initiative-dsh-dashboard/prd-dsh-2026-11-15/prd-dsh-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-dsh-dashboard/prd-dsh-2026-11-15/prd-dsh-2026-11-15.md
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

- R1 (DSH.FR-1; parent R37): The dashboard shows one period of the active organization, chosen in a selector listing its periods newest first and defaulting to the period containing today, else the most recent; the choice is kept in the page address and shared with Activities and Reports, with no "All periods" option.
- R2 (DSH.FR-2; parent R34, R31): Four cards show total, electricity, fuel and business travel emissions for the period in tonnes CO2e to 2 decimals, each type with its whole-percentage share and 0.00 t and 0% when empty, and the disclaimer is visible.
- R3 (DSH.FR-3; parent R35): The trend chart shows one stacked bar per calendar month across the whole period, zero for months with no data (future months included), labelled by month with the year across a year boundary, each type distinguishable without colour.
- R4 (DSH.FR-4; parent R36): A donut chart shows the three types' shares of the period total with the total in its centre, listing a type with no activity at 0%.
- R5 (DSH.FR-5; parent R38): Cards and charts load separately with skeletons; an empty period keeps the cards at 0 and shows "Add activity data", which opens M2's add form; an organization with no periods leads to adding one; a failure shows Retry in its own area only; a save elsewhere shows on the next view without a manual refresh.
- R6 (DSH.FR-6; parent Constraints NFR-04): Each chart has a keyboard-reachable table view of its numbers and a screen-reader summary naming the period, the total and the largest type.
- R7 (DSH.FR-7, Should; parent R39): Hovering or focusing a bar or segment shows its exact value, unit and month or type; clicking a legend item hides or shows that type and rescales the chart, resetting when the period changes; both work by keyboard.
- R8 (DSH.FR-8, Could; parent R40): Each card shows the absolute and percentage change from the period that ended just before the selected one, marked favourable or unfavourable beyond colour, and shows none for the earliest period, across a gap, or when that period has no activity.

## Constraints

- Every figure comes from M3; the dashboard calculates nothing, so it always matches the headline figures and the reports (EMI.FR-5, EMI.FR-6).
- Charts never rely on colour alone, and each activity type has one design-token colour (NFR-04, architecture §10.4).
- Cards show tonnes and tables kilograms, both to 2 decimals (master PRD §6, Q-11).
- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).

## Done when

- SM-1: Ten-second read: Total, shares and trend are visible without scrolling on a 1366 px wide screen.
- SM-2: One source of numbers: The dashboard total equals the organization's headline total (ORG.FR-5) and the report summary total (FR-RPT-01) for the same period and data.
- SM-3: Fast enough: The dashboard's data loads within NFR-02's limit with the 50,000-row dataset.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 2 for the organization's periods (parent tickets.toml).
- Waits on child 3 for totals and monthly aggregates (parent tickets.toml).
- Waits on child 4 for the add-activity form the empty state opens (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R34–R40
- prd — docs/initiative-dsh-dashboard/prd-dsh-2026-11-15/prd-dsh-2026-11-15.md, §4 Features (adopted companion: each FR's testable consequences)
- addendum — docs/initiative-dsh-dashboard/prd-dsh-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
