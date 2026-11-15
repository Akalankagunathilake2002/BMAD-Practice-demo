---
tracker_id: ""
type: initiative
title: "Activity data: activities are entered quickly and are hard to get wrong"
parent: none
sources:
  - docs/initiative-act-activity-data/prd-act-2026-11-15/prd-act-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-act-activity-data/prd-act-2026-11-15/addendum.md
after: []
risk: low
---

# Activity data: activities are entered quickly and are hard to get wrong

## Description

Users record, review, correct and delete activities on M3's activity table, with each save calling M3's calculation in the same transaction. M2 also refuses period edits that would strand activities, and seeds the demo organizations, periods and activities. Child 4 of the parent initiative `initiative-carbontrack`; its requirements come from the M2 module PRD, with the moves recorded in Notes.

## Outcome

An operations coordinator enters each bill or trip as one activity without choosing units or periods, and fixes mistakes in place.

## Requirements

- R1 (ACT.FR-1, MVP; parent R17, R23): Add an activity; the save calls M3's calculation (Decision A)
- R2 (ACT.FR-2, MVP; parent R18): Validation
- R3 (ACT.FR-3, MVP; parent R19): Link to the reporting period
- R4 (ACT.FR-4, MVP; parent R20): Activity list
- R5 (ACT.FR-5, MVP; parent R20, R25, R31): Show the calculation, factor and disclaimer on each row
- R6 (ACT.FR-6, MVP; parent R21): Edit an activity; M3 recalculates it
- R7 (ACT.FR-7, MVP; parent R16): Open the list pre-filtered from a link
- R8 (ACT.FR-8, Should; parent R22): Delete an activity
- R9 (ACT.FR-9, Could; parent R33): Scope label on each row
- R10 (ORG.FR-8, MVP; parent R16): Refuse a period edit that would leave activities outside the period, saying how many (moved from M1, Decision B)
- R11 (PLT.FR-11, MVP; parent R4): Seed the demo organizations, calendar-year periods and fixed-date activities (moved from M0, Decision B)

Parent requirements this child delivers all or part of: R4, R16, R17, R18, R19, R20, R21, R22, R23, R25, R31, R33.

## Constraints

- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).
- Module PRD §0 names the master NFRs that matter most here.

## Done when

- SM-1: Fast entry: An activity is added in one dialog with three required fields, with no period or unit to choose.
- SM-2: Every row explainable: Every row in the list shows its emissions and the factor used.
- SM-3: Clean rejects: Every invalid save is refused by the server with a field-level error, including saves that bypass the form.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 2 for reporting periods to link activities to (parent tickets.toml).
- Waits on child 3 for the activity table and the calculation called on every save (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R17–R22
- prd — docs/initiative-act-activity-data/prd-act-2026-11-15/prd-act-2026-11-15.md, §4 Features
- addendum — docs/initiative-act-activity-data/prd-act-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
