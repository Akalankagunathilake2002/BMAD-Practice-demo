---
tracker_id: ""
type: initiative
title: "Platform foundation: anyone starts the demo and moves around a professional app"
parent: none
sources:
  - docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/prd-plt-2026-11-14.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/addendum.md
after: []
risk: medium
---

# Platform foundation: anyone starts the demo and moves around a professional app

## Description

One command starts the app with demo sign-in, the app shell and organization switcher, and the shared error and page-state behavior every module uses. The seed command and demo users start here; factor and demo-data seeding join it in M3 and M2. Child 1 of the parent initiative `initiative-carbontrack`; its requirements come from the M0 module PRD, with the moves recorded in Notes.

## Outcome

A demo evaluator gets a working, seeded app from a clean machine, and every user signs in and moves around one consistent shell.

## Requirements

- R1 (PLT.FR-1, MVP; parent R1): App shell: sidebar, top bar, drawer on narrow screens
- R2 (PLT.FR-2, MVP; parent R13): Organization switcher in the top bar (M1 owns the organization list)
- R3 (PLT.FR-3, MVP; parent R1): Page addresses, `/` redirect and not-found page
- R4 (PLT.FR-4, MVP; parent R7, R8): Sign in and return to the requested page
- R5 (PLT.FR-5, MVP; parent R9): Session of 8 hours and sign-out
- R6 (PLT.FR-6, MVP; parent R10): Demo accounts, one per role
- R7 (PLT.FR-7, Should; parent R11): Session token required on every API call
- R8 (PLT.FR-8, MVP; parent R2): API documentation
- R9 (PLT.FR-9, MVP; parent R3): Health check
- R10 (PLT.FR-10, Should; parent R6): Reference-data endpoint with activity types and countries (M1 adds the industry list). M0 creates the activity-type list with units (shared setup); M3 adds factors and scope labels to it
- R11 (PLT.FR-11, MVP; parent R4): Seed command, repeatable, creating the demo users. Factor version 1 is seeded in M3 and demo data in M2 (Decision B)
- R12 (PLT.FR-12, MVP; parent R5): Start with a copied `.env` and `docker compose up`
- R13 (PLT.FR-13, MVP; parent parent Constraints NFR-05): Error envelope and request IDs
- R14 (PLT.FR-14, MVP; parent R38): Shared loading, empty and error components used by every module (M4 owns the dashboard's states)

Parent requirements this child delivers all or part of: R1, R2, R3, R4, R5, R6, R7, R8, R9, R10, R11, R13, R38.

## Constraints

- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).
- Module PRD §0 names the master NFRs that matter most here.

## Done when

- SM-1: Clean-machine start.: Following the README on a clean machine gives a working, seeded app with no other steps.
- SM-2: Repeatable seed.: Running the seed command a second time changes no rows.
- SM-3: Exact demo totals.: The seeded totals match the golden values exactly (NFR-01).

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R1–R11
- prd — docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/prd-plt-2026-11-14.md, §4 Features
- addendum — docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
