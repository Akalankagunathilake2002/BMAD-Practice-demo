---
tracker_id: ""
type: initiative
title: "Activity data: activities are entered quickly and are hard to get wrong"
parent: none
sources:
  - docs/initiative-act-activity-data/prd-act-2026-11-15/prd-act-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-act-activity-data/prd-act-2026-11-15/addendum.md
after: []
risk: low
---

# Activity data: activities are entered quickly and are hard to get wrong

## Description

Users record, review, correct and delete activities; the unit follows the type, the period follows the date, and each row shows its emissions. Child 4 of the parent initiative `initiative-carbontrack`; its requirements come from the M2 module PRD.

## Outcome

An operations coordinator enters each bill or trip as one activity without choosing units or periods, and fixes mistakes in place.

## Requirements

- R1 (ACT.FR-1, MVP): Add an activity
- R2 (ACT.FR-2, MVP): Validation
- R3 (ACT.FR-3, MVP): Link to the reporting period
- R4 (ACT.FR-4, MVP): Activity list
- R5 (ACT.FR-5, MVP): Show the calculation on each row
- R6 (ACT.FR-6, MVP): Edit an activity
- R7 (ACT.FR-7, MVP): Open the list pre-filtered from a link
- R8 (ACT.FR-8, Should): Delete an activity
- R9 (ACT.FR-9, Should): Scope label on each row

Parent requirements this child covers: R17, R18, R19, R20, R21, R22.

## Constraints

- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).
- Module PRD §0 names the master NFRs that matter most here.

## Done when

- SM-1: Fast entry.: An activity is added in one dialog with three required fields, with no period or unit to choose.
- SM-2: Every row explainable.: Every row in the list shows its emissions and the factor used.
- SM-3: Clean rejects.: Every invalid save is refused by the server with a field-level error, including saves that bypass the form.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 2 for reporting periods to link activities to (parent tickets.toml).
- Waits on child 3 for calculation on every save (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R17–R22
- prd — docs/initiative-act-activity-data/prd-act-2026-11-15/prd-act-2026-11-15.md, §4 Features
- addendum — docs/initiative-act-activity-data/prd-act-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership
