---
tracker_id: ""
type: initiative
title: "Platform foundation: anyone starts the demo and moves around a professional app"
parent: none
sources:
  - docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/prd-plt-2026-11-14.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/prd-plt-2026-11-14.md
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

- R1 (PLT.FR-1; parent R1): Every signed-in page shows the sidebar and top bar; the sidebar becomes a drawer on narrow screens, works fully by keyboard with visible focus, and hides the Emission Factors entry while that page is not built.
- R2 (PLT.FR-2; parent R13): Switching organization keeps the user on the same page for the new organization and resets the selected period to its default; with no organizations the switcher offers "Create organization".
- R3 (PLT.FR-3; parent R1): Every page has its own address; `/` opens the last-used organization's dashboard or, with none, the organization list; an unknown address or organization shows a not-found page.
- R4 (PLT.FR-4; parent R7, R8): A visitor signs in with a listed demo account and lands on the in-app page they asked for; a failed sign-in does not say which field was wrong, and the password is never shown.
- R5 (PLT.FR-5; parent R9): A signed-in user stays signed in across reloads until 8 hours after sign-in, with no extension; an expired session returns them to sign-in with "Your session has expired", then to the same page.
- R6 (PLT.FR-6; parent R10): The seed creates one demo account per role on example.com addresses, sharing one password from the environment and stored only as a hash; roles restrict nothing.
- R7 (PLT.FR-7, Should; parent R11): Without a valid token, every API call except health, sign-in and the docs gets a 401 saying whether the token was missing, invalid or expired; the docs' Authorize accepts a token.
- R8 (PLT.FR-8; parent R2): Interactive API documentation and the schema are served, and every endpoint has a summary, a description and an example.
- R9 (PLT.FR-9; parent R3): The health check works without a token and returns 200 with each part's status when the API and database are up, 503 when the database is unreachable.
- R10 (PLT.FR-10, Should; parent R6): One reference-data call returns activity types with units, and countries; M0 creates the activity-type list, M1 adds industries and M3 adds scope labels.
- R11 (PLT.FR-11; parent R4): Running the seed command twice changes nothing and keeps users' edits; it creates the demo accounts when demo seeding is on, and M3 and M2 extend the same command with factors and demo data (Decision B).
- R12 (PLT.FR-12; parent R5): On a clean machine with only Docker, copying .env.example to .env and running docker compose up starts a migrated, seeded app reachable only from that machine; a missing .env stops the start with a message naming the copy step.
- R13 (PLT.FR-13; parent Constraints NFR-05): Every API error carries a code, message, field details and request ID, and every response has a request ID; unexpected errors show a generic message, never a stack trace; the UI shows each error with a recovery action and the request ID.
- R14 (PLT.FR-14; parent R38): Shared loading, error-with-Retry and empty-with-one-action components exist, and every data view in M1 to M5 uses them.

## Constraints

- Sign-in is demonstration only: no registration, password reset, role enforcement or server-side sign-out (FR-AUTH-06, architecture §6.5).
- No secret or fallback value in committed files; all configuration comes from the environment, with a placeholder-only .env.example (NFR-06).
- The seed runs on every start, so it must never change data that already exists (PLT.FR-11).
- The API accepts browser requests only from configured origins, and every port listens on the local machine only (NFR-06, architecture §12.2).
- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).

## Done when

- SM-1: Clean-machine start.: Following the README on a clean machine gives a working, seeded app with no other steps.
- SM-2: Repeatable seed.: Running the seed command a second time changes no rows.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R1–R11
- prd — docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/prd-plt-2026-11-14.md, §4 Features (adopted companion: each FR's testable consequences)
- addendum — docs/initiative-plt-platform-foundation/prd-plt-2026-11-14/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
