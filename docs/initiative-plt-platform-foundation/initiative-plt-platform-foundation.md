---
tracker_id: ""
type: initiative
title: "Platform foundation: anyone starts the demo and moves around a professional app"
parent: none
sources:
  - docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/prd-plt-2026-11-14.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/addendum.md
after: []
risk: medium
---

# Platform foundation: anyone starts the demo and moves around a professional app

## Description

One command starts a seeded app with demo sign-in, the app shell, and the shared error and page-state behavior every module uses. Child 1 of the parent initiative `initiative-carbontrack`; its requirements come from the M0 module PRD.

## Outcome

A demo evaluator gets a working, seeded app from a clean machine, and every user signs in and moves around one consistent shell.

## Requirements

- R1 (PLT.FR-1, MVP): App shell
- R2 (PLT.FR-2, MVP): Switch organization
- R3 (PLT.FR-3, MVP): Addresses, redirects and not-found
- R4 (PLT.FR-4, MVP): Sign in and return
- R5 (PLT.FR-5, MVP): Session and sign-out
- R6 (PLT.FR-6, MVP): Demo accounts
- R7 (PLT.FR-7, Should): Token on every API call
- R8 (PLT.FR-8, MVP): API documentation
- R9 (PLT.FR-9, MVP): Health check
- R10 (PLT.FR-10, Should): Reference data
- R11 (PLT.FR-11, MVP): Seed command
- R12 (PLT.FR-12, MVP): One-command start
- R13 (PLT.FR-13, MVP): Errors and request IDs
- R14 (PLT.FR-14, MVP): Shared page states

Parent requirements this child covers: R1, R2, R3, R4, R5, R6, R7, R8, R9, R10, R11.

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
