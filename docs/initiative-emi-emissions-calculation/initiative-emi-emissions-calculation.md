---
tracker_id: ""
type: initiative
title: "Emissions calculation: every figure is explainable and never silently rewritten"
parent: none
sources:
  - docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/prd-emi-2026-11-14.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/prd-emi-2026-11-14.md
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack-detail.md
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

- R1 (EMI.FR-1; parent R23, R21): Saving an activity, created or edited (even only its description), creates a new current calculation with the active factor in the same save; with no active factor for its type the save is refused naming the type, and nothing is stored.
- R2 (EMI.FR-2; parent R24): Each activity has exactly one current calculation and keeps every earlier one, never edited, oldest first; totals use current calculations only.
- R3 (EMI.FR-3; parent R25): Every API response containing a calculation carries the factor version it used (value, unit, version, source label), and each history entry carries its own.
- R4 (EMI.FR-4; parent R31): Every source label, shown or returned, contains the demonstration disclaimer, whatever the user typed.
- R5 (EMI.FR-5; parent R26, R27): For one period or overall, M3 returns the total and each of the three activity types' emissions and share; a type with no data shows 0 and 0%, and a zero total raises no error.
- R6 (EMI.FR-6; parent R28): For one period, M3 returns totals for every calendar month of the period as YYYY-MM, overall and by type, oldest first, with zeros for months without activity, within NFR-02's response time (architecture AD-9).
- R7 (EMI.FR-7; parent R29): A user adds a factor version, value 0 to 999,999.999999 with at most 6 decimals and optional source text up to 100 characters, that becomes active at once for every organization; older versions stay unchanged and undeletable; a simultaneous add for the same type gets a conflict error.
- R8 (EMI.FR-8, Should; parent R30, R1): The Emission Factors page, behind M0's sidebar entry, shows each type's active factor and its version history newest first with the date added, the add-version form and the disclaimer.
- R9 (EMI.FR-9, Should; parent R32): Before recalculating an organization the user sees how many calculations use an older factor and confirms; all are replaced in one all-or-nothing step and the user sees how many changed, or "All results are up to date" when none are out of date.
- R10 (EMI.FR-10, Could; parent R33): Each activity type carries its informational scope label in M0's activity-type list, and the Emission Factors page shows it.
- R11 (architecture §8.2; parent R17): The activity table exists with each activity linked to one period of its organization, so the calculation core is tested with stored activities before M2 adds recording (Decision A).
- R12 (PLT.FR-11; parent R4): The seed command creates factor version 1 for each activity type only when that type has none, and running it again changes nothing.
- R13 (ORG.FR-5, Should; parent R14): The organization details page shows the activity count and total emissions across all periods, matching M3's overall total, with 0 and no error when there is no data.

## Constraints

- Emission arithmetic and storage use exact decimals, rounded once to 6 places half-up, never binary floating point (master PRD §6, NFR-01).
- Factor versions never change and apply to every organization: one active version per type, with no regional, yearly or per-organization factors (A-08, FR-EMI-07).
- A factor change alone never alters a stored calculation; only an edit or an explicit recalculation does (A-09, Q-06).
- Factor values are demonstration values and are never presented as official (FR-EMI-09).
- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).

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
- prd — docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/prd-emi-2026-11-14.md, §4 Features (adopted companion: each FR's testable consequences)
- addendum — docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, AD-1–AD-18 (the contract; AD-2 ownership, AD-17 seams)
- architecture detail — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack-detail.md, for anything no AD covers

## Notes

- Source conflict: EMI.FR-6 — the module PRD returns only months with data; the ticket returns every month of the period.
- Decision: 2026-11-15, months are filled once on the server (architecture AD-9); R6 updated, the PRD stays as written.
- Decision: 2026-11-15, the architecture spine covers these: out-of-date count (AD-12), source text and created_at (AD-6), activity table schema owner M3 with rows written by M2 (AD-2).
- Decision: 2026-11-15, the PRD's open question on the sidebar link vs the Should page is settled by M0 R1: the entry is hidden until the page exists.
- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
