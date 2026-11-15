---
tracker_id: ""
type: initiative
title: "Emissions calculation: every figure is explainable and never silently rewritten"
parent: none
sources:
  - docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/prd-emi-2026-11-14.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/addendum.md
after: []
risk: medium
---

# Emissions calculation: every figure is explainable and never silently rewritten

## Description

M3 builds the activity table and the calculation core and tests them directly: factors and their versions, calculation with history, the factor returned with every calculation, and totals by type and month. M2 then builds what users do with activities on top and calls the calculation in the same save. M3 also seeds factor version 1 and adds the organization's headline figures. Child 3 of the parent initiative `initiative-carbontrack`; its requirements come from the M3 module PRD, with the moves recorded in Notes.

## Outcome

Every reader can trace any emission figure to its activity, factor version and quantity, even after factors change.

## Requirements

- R1 (EMI.FR-1, MVP; parent R23, R21): Calculate on every save: the calculation service M2's save calls (Decision A)
- R2 (EMI.FR-2, MVP; parent R24): Keep the calculation history
- R3 (EMI.FR-3, MVP; parent R25): Return the factor version with every calculation (M2, M4 and M5 show it)
- R4 (EMI.FR-4, MVP; parent R31): Keep the demonstration disclaimer in every source label (M2, M4 and M5 show it)
- R5 (EMI.FR-5, MVP; parent R26, R27): Total and share by activity type
- R6 (EMI.FR-6, MVP; parent R28): Monthly totals
- R7 (EMI.FR-7, MVP; parent R29): Add a factor version
- R8 (EMI.FR-8, Should; parent R30, R1): Emission Factors page, behind M0's sidebar entry
- R9 (EMI.FR-9, Should; parent R32): Recalculate an organization
- R10 (EMI.FR-10, Could; parent R33): GHG scope label per activity type, added to M0's activity-type list (M2 and M5 show it)
- R11 (architecture §8.2, MVP; parent R17): The activity table and its link to a period, built here so the calculation core can be tested (Decision A); M2 builds recording on it
- R12 (PLT.FR-11, MVP; parent R4): Seed factor version 1 for each activity type (moved from M0, Decision B)
- R13 (ORG.FR-5, Should; parent R14): Headline figures on the organization details page: activity count and total, all periods (moved from M1, Decision B)

Parent requirements this child delivers all or part of: R1, R4, R14, R17, R21, R23, R24, R25, R26, R27, R28, R29, R30, R31, R32, R33.

## Constraints

- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).
- Module PRD §0 names the master NFRs that matter most here.

## Done when

- SM-1: Exact totals: The golden-dataset test (NFR-01) matches its expected totals by type and by month exactly.
- SM-2: Every number explainable: 100% of the emission calculations returned by the API carry their factor version, and 100% of source labels contain the demonstration disclaimer.
- SM-3: Aggregates are fast: The summary and monthly responses meet NFR-02.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 1 for the platform foundation and seed command (parent tickets.toml).
- Waits on child 2 for reporting periods for totals per period (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R23–R33
- prd — docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/prd-emi-2026-11-14.md, §4 Features
- addendum — docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
