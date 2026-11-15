---
tracker_id: ""
type: initiative
title: "Activity data: activities are entered quickly and are hard to get wrong"
parent: none
sources:
  - docs/initiative-act-activity-data/prd-act-2026-11-15/prd-act-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-act-activity-data/prd-act-2026-11-15/prd-act-2026-11-15.md
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack-detail.md
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

- R1 (ACT.FR-1; parent R17, R23): A user adds an activity in a dialog with type, quantity, date and optional description, the unit following the type; the save calls M3's calculation in the same step, the new row shows its emissions and factor, and when the activity falls outside the selected period the confirmation offers to switch to its period.
- R2 (ACT.FR-2; parent R18): The server refuses, each with its error at its field, a quantity of 0 or less, over 1,000,000,000 or with more than 3 decimals, a date after today or outside every period, a description over 500 characters, a unit not matching the type, and a type with no active factor.
- R3 (ACT.FR-3; parent R19): Each saved activity is linked to the one period of its organization that contains its date; the user never chooses the period.
- R4 (ACT.FR-4; parent R20): The Activities page opens on the selected period with an "All periods" option, filters by type, period and date range (a date range overriding the period), sorts by date (newest first by default), type, quantity or emissions, pages 25 rows by default and up to 100 with the total shown, and keeps filters and sort in its address.
- R5 (ACT.FR-5; parent R20, R25, R31): Each row shows date, type, quantity with unit, description, emissions in kg CO2e to 2 decimals and the factor used (value, unit, version); the page shows the disclaimer, and each row's calculation history opens from it.
- R6 (ACT.FR-6; parent R21): Editing changes type, quantity, date or description under R2's rules; a type change switches the unit, keeps the number and says the unit changed; a new date in another period moves the activity there; every saved edit is recalculated, and the last save wins.
- R7 (ACT.FR-7; parent R16): The Activities page opens with a period and a date range from its address applied; M1's refused period edit links here.
- R8 (ACT.FR-8, Should; parent R22): Delete asks for confirmation naming the activity's date, type and quantity and saying there is no undo, then removes the activity and its calculation history from every total.
- R9 (ACT.FR-9, Could; parent R33): Each row shows its activity type's informational scope label from the reference data.
- R10 (ORG.FR-8; parent R16): A period edit that would leave any of its activities outside its new dates is refused, with the count and a link to them (R7).
- R11 (PLT.FR-11; parent R4): With demo seeding on, the seed command creates 2 fictional organizations with calendar-year periods and fixed-date activities from May 2025 to October 2026, all passing the app's own validation, and leaves existing organizations untouched when run again.

## Constraints

- An activity's period always follows its date, and no activity is ever outside a period (A-04).
- One unit per activity type, with no conversion (A-06, Q-05).
- The server is the authority on validation; the form only gives early feedback (architecture §12.3, NFR-06).
- Demo data uses fixed dates and quantities so its totals are the NFR-01 golden values; moving the date window means updating the seed and the golden values together (PLT.FR-11, moved from M0 by Decision B).
- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).

## Done when

- SM-1: Fast entry: An activity is added in one dialog with three required fields, with no period or unit to choose.
- SM-2: Every row explainable: Every row in the list shows its emissions and the factor used.
- SM-3: Clean rejects: Every invalid save is refused by the server with a field-level error, including saves that bypass the form.
- SM-4: Exact demo totals: The seeded demo totals match the golden values exactly (NFR-01; moved from M0 by Decision B).
- SM-5: No stranded activities: After any sequence of period and activity edits, no activity lies outside its period (moved from M1 by Decision B).

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 2 for reporting periods to link activities to (parent tickets.toml).
- Waits on child 3 for the activity table and the calculation called on every save (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R17–R22
- prd — docs/initiative-act-activity-data/prd-act-2026-11-15/prd-act-2026-11-15.md, §4 Features (adopted companion: each FR's testable consequences)
- addendum — docs/initiative-act-activity-data/prd-act-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, AD-1–AD-18 (the contract; AD-2 ownership, AD-17 seams)
- architecture detail — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack-detail.md, for anything no AD covers

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
