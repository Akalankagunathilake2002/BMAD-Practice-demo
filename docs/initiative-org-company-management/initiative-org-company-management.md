---
tracker_id: ""
type: initiative
title: "Company management: every activity sits inside one period of its organization"
parent: none
sources:
  - docs/initiative-org-company-management/prd-org-2026-11-15/prd-org-2026-11-15.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-org-company-management/prd-org-2026-11-15/prd-org-2026-11-15.md
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

- R1 (ORG.FR-1; parent R12): A user creates an organization and its first reporting period in one form, both saved or neither; a name already used, ignoring case and outer spaces, is refused on the name field, and the new organization's empty dashboard opens.
- R2 (ORG.FR-2; parent R12, R6): Industry is chosen from a fixed list of 12 values (Q-16); the server refuses any other value, and the UI gets the list through reference data.
- R3 (ORG.FR-3; parent R13): The organization list shows name, industry and country sorted by name and offers "Create organization"; choosing one opens its dashboard, and the switcher lists the same organizations in the same order.
- R4 (ORG.FR-4; parent R14): The organization details page shows the profile and every reporting period with its dates, newest first, with actions to edit the profile and add or edit a period.
- R5 (ORG.FR-6; parent R15): Editing name, industry or country applies the creation rules and allows a change of letter case alone; the new name shows everywhere at once, and no emission figure changes.
- R6 (ORG.FR-7; parent R16): A period needs a name of up to 50 characters, unique in the organization ignoring case, and an end date on or after its start; an overlapping period is refused naming the period it clashes with; gaps and future periods are allowed.
- R7 (ORG.FR-8; parent R16): A user changes a period's name or dates under the R6 rules, and an accepted edit never moves an activity or changes a figure; refusing an edit that would strand activities is M2's (Decision B).

## Constraints

- Every activity always falls inside exactly one period of its organization; no edit may break that (A-03, A-04).
- Organizations and reporting periods are never deleted (FR-ORG-06; module decision "add and edit only").
- Every demo account sees and edits every organization: roles are labels only and organizations are not isolated (A-01, A-02).
- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).

## Done when

- SM-1: One-form onboarding: A new organization, with its first period, is created in one form submission, after which its empty dashboard opens.
- SM-2: Period integrity: After any sequence of period adds and edits, no two periods of an organization overlap.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 1 for the app shell, sign-in and shared page states (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R12–R16
- prd — docs/initiative-org-company-management/prd-org-2026-11-15/prd-org-2026-11-15.md, §4 Features (adopted companion: each FR's testable consequences)
- addendum — docs/initiative-org-company-management/prd-org-2026-11-15/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership

## Notes

- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
