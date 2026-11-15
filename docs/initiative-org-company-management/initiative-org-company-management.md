---
tracker_id: ""
type: initiative
title: "Company management: every activity sits inside one period of its organization"
parent: none
sources:
  - docs/initiative-org-company-management/prd-org-2026-11-15/prd-org-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-org-company-management/prd-org-2026-11-15/addendum.md
after: []
risk: low
---

# Company management: every activity sits inside one period of its organization

## Description

Users create and switch organizations and keep their reporting periods correct, and no edit can leave an activity outside its period. Child 2 of the parent initiative `initiative-carbontrack`; its requirements come from the M1 module PRD.

## Outcome

A sustainability manager sets up an organization and its periods in one form and can trust that every activity is counted in exactly one period.

## Requirements

- R1 (ORG.FR-1, MVP): Create an organization
- R2 (ORG.FR-2, MVP): Industry list
- R3 (ORG.FR-3, MVP): List organizations
- R4 (ORG.FR-4, MVP): Organization details
- R5 (ORG.FR-5, Should): Headline figures
- R6 (ORG.FR-6, MVP): Edit the profile
- R7 (ORG.FR-7, MVP): Add a reporting period
- R8 (ORG.FR-8, MVP): Edit a reporting period

Parent requirements this child covers: R12, R13, R14, R15, R16.

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
