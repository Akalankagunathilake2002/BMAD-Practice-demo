---
tracker_id: ""
type: initiative
title: "Company management: every activity sits inside one period of its organization"
parent: none
sources:
  - docs/initiative-org-company-management/prd-org-2026-11-15/prd-org-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-org-company-management/prd-org-2026-11-15/addendum.md
after: []
risk: low
---

# Company management: every activity sits inside one period of its organization

## Description

Users create, list, switch and edit organizations and add and edit their reporting periods. The organization switcher's container is M0's. Headline figures and the period-edit guard are built later, in M3 and M2. Child 2 of the parent initiative `initiative-carbontrack`; its requirements come from the M1 module PRD, with the moves recorded in Notes.

## Outcome

A sustainability manager sets up an organization and its periods in one form and can trust that every activity is counted in exactly one period.

## Requirements

- R1 (ORG.FR-1, MVP; parent R12): Create an organization with its first reporting period
- R2 (ORG.FR-2, MVP; parent R12, R6): Industry list (Q-16), served through M0's reference data
- R3 (ORG.FR-3, MVP; parent R13): List organizations; the list behind M0's switcher
- R4 (ORG.FR-4, MVP; parent R14): Organization details: profile and periods (headline figures are M3's, Decision B)
- R5 (ORG.FR-6, MVP; parent R15): Edit the profile
- R6 (ORG.FR-7, MVP; parent R16): Add a reporting period: unique name, no overlap, gaps allowed
- R7 (ORG.FR-8, MVP; parent R16): Edit a reporting period's name and dates. Refusing an edit that strands activities is M2's (Decision B)

Parent requirements this child delivers all or part of: R6, R12, R13, R14, R15, R16.

## Constraints

- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).
- Module PRD §0 names the master NFRs that matter most here.

## Done when

- SM-1: One-form onboarding.: A new organization, with its first period, is created in one form submission, after which its empty dashboard opens.
- SM-2: Period integrity.: After any sequence of period adds and edits, no two periods of an organization overlap and no activity lies outside its period.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 1 for the app shell, sign-in and shared page states (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R12–R16
- prd — docs/initiative-org-company-management/prd-org-2026-11-15/prd-org-2026-11-15.md, §4 Features
- addendum — docs/initiative-org-company-management/prd-org-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
