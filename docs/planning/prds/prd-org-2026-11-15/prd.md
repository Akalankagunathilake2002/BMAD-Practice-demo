---
title: "CarbonTrack — Module PRD: Company Management (M1)"
status: final
created: 2026-11-15
updated: 2026-11-15
module: M1
slug: org
parent: docs/planning/prd.md
---

# CarbonTrack — Module PRD: Company Management (M1)

| | |
|---|---|
| Module | M1 Company Management, slug `org` |
| Status | Final, awaiting CP3 approval (merge of the CP3 pull request, Q-17) |
| Scope | 8 FRs: ORG.FR-1 to 4 and 6 to 8 MVP; ORG.FR-5 Should (§6) |
| Parent | [Master PRD v0.2](../../prd.md) (approved at CP1) |
| Constraints from | [Master Architecture v0.2](../../architecture.md) (approved at CP2) |
| Related | [M0 module PRD](../prd-plt-2026-11-14/prd.md) (switcher, seed) · [M3 module PRD](../prd-emi-2026-11-14/prd.md) (totals) |
| Checkpoint | CP3 Module PRDs |
| Decision log | [.memlog.md](.memlog.md) · Supporting detail in [addendum.md](addendum.md) |

## 0. Document purpose

This PRD details what M1 must do so that UX (CP4) and epics and stories (CP5) can be written from it. It does not repeat the master PRD. Where a rule already exists there, in the architecture or in another module PRD, this document cites its ID (for example FR-ORG-05, A-04, Q-16, PLT.FR-2, EMI.FR-5) and adds only what is left open.

New functional requirements are numbered **ORG.FR-1** onward. Each carries a tag:

- **MVP**: refines a Must requirement in the master PRD, so it is part of the MVP (master §9).
- **Should**: refines a master requirement with that priority.

Rules inferred where the source documents are silent are marked `[ASSUMPTION]` and indexed in §9. All of them were accepted at the draft review on 2026-11-15. The tags stay so that story refinement can trace them.

All master non-functional requirements apply to M1 unchanged. NFR-05 (field-level errors) and NFR-08 (tests for business rules) matter most here.

This PRD settles **Q-16**: industry is chosen from a predefined list (ORG.FR-2).

## 1. Vision

M1 sets up the frame that every carbon figure sits in: the organization it belongs to and the reporting period it is counted in. Getting this right is what keeps reports complete. Every activity always falls inside exactly one period of its organization (A-03, A-04), and no edit can break that.

M1 also opens the product. Creating an organization is the first step of journey J1, and it should take one form, after which the user lands on their empty dashboard.

## 2. Users and journeys

The personas are defined in [project brief §3](../../../analysis/project-brief.md#3-users-and-stakeholders). M1 is used mainly by the **Sustainability Manager**, who sets up the organization and its periods and keeps them correct. Every demo account can do this, because roles are labels only (A-01).

M1 realizes the core of master journey **J1** (onboard an organization). Its periods are the selection that J3 (review and report) works within.

## 3. Glossary

*Organization* and *reporting period* keep their master meanings, and *active organization* is defined in the M0 PRD. This PRD adds:

- **Industry list.** The fixed list of industries an organization chooses from (ORG.FR-2).
- **Profile.** An organization's name, industry and country.
- **Headline figures.** An organization's activity count and total emissions across all its periods (ORG.FR-5).
- **Gap.** A date range between two of an organization's periods that no period covers. Activities can't be dated in a gap (A-04).

## 4. Features

### 4.1 Organizations

Realizes J1. Refines FR-ORG-01 to FR-ORG-04.

#### ORG.FR-1 · MVP: Create an organization

A user creates an organization and its first reporting period in one form. Refines FR-ORG-01 and A-03.

**Consequences (testable):**
- The organization and its first period are saved together, or not at all.
- The first period follows the same rules as any other period (ORG.FR-7).
- `[ASSUMPTION]` Leading and trailing spaces are removed from the name before it is checked.
- A name already used by another organization, ignoring case, is refused with an error on the name field. If two people create the same name at the same moment, one succeeds and the other gets that error.
- `[ASSUMPTION]` The first-period fields start filled with the current calendar year, named by its year (for example "2026"). This matches the demo data (PLT.FR-11). The user can change them.
- After creation, the new organization becomes the active organization and its empty dashboard opens, with the prompt to add activity data (J1, PLT.FR-14).

#### ORG.FR-2 · MVP: Industry list

Settles Q-16 as option (a): industry is chosen from a predefined list (A-14).

**Consequences (testable):**
- `[ASSUMPTION]` The list has 12 values: Manufacturing; Retail and Wholesale; Technology; Financial Services; Healthcare; Education; Hospitality; Transport and Logistics; Construction and Real Estate; Energy and Utilities; Professional Services; Other.
- The server refuses any industry that is not on the list.
- The UI and the server always use the same list. The UI gets it from the reference data (PLT.FR-10).

#### ORG.FR-3 · MVP: List organizations

Refines FR-ORG-02. Every demo account sees every organization (A-02).

**Consequences (testable):**
- `[ASSUMPTION]` The organization list shows each organization's name, industry and country, sorted by name ignoring case.
- Choosing an organization from the list makes it the active organization and opens its dashboard.
- The list has a "Create organization" action.
- With no organizations, the list shows the empty state with "Create organization" as its main action (PLT.FR-14).
- The organization switcher in the top bar (PLT.FR-2) lists the same organizations in the same order.

#### ORG.FR-4 · MVP: Organization details

Refines FR-ORG-03. Reached from the Organization entry in the sidebar.

**Consequences (testable):**
- The page shows the organization's profile and all its reporting periods, each with its name, start date and end date.
- `[ASSUMPTION]` Periods are listed newest first, by start date.
- The page offers editing the profile (ORG.FR-6), adding a period (ORG.FR-7) and editing a period (ORG.FR-8).

#### ORG.FR-5 · Should: Headline figures

The organization details page shows the organization's activity count and total emissions. Refines the Should part of FR-ORG-03.

**Consequences (testable):**
- Both figures cover **all of the organization's periods combined**.
- The total is M3's overall total for the organization (EMI.FR-5), so it always matches the dashboard and reports for the same data.
- An organization with no activities shows 0 for both figures, not an error.

#### ORG.FR-6 · MVP: Edit the profile

Refines FR-ORG-04.

**Consequences (testable):**
- Name, industry and country follow the same rules as at creation (ORG.FR-1, ORG.FR-2).
- The name check ignores the organization itself, so changing only the letter case of a name is allowed.
- A new name appears at once in the top bar, the switcher and the organization list.
- Changing industry or country changes no emission figure, because factors do not depend on them (A-08).

### 4.2 Reporting periods

Refines FR-ORG-05, A-03 and A-04. Periods can be added and edited, never deleted (§5).

#### ORG.FR-7 · MVP: Add a reporting period

**Consequences (testable):**
- A period needs a name (up to 50 characters), a start date and an end date.
- `[ASSUMPTION]` Period names are trimmed, and must be unique within the organization ignoring case.
- The end date may equal the start date (a one-day period), but may not be before it.
- A period that overlaps any other period of the same organization is refused, with an error naming the period it overlaps.
- Gaps between periods are allowed. Activities dated in a gap are refused by M2 (A-04, FR-ACT-02).
- `[ASSUMPTION]` Periods may lie partly or wholly in the future, and have no maximum length.

#### ORG.FR-8 · MVP: Edit a reporting period

A user changes a period's name or dates.

**Consequences (testable):**
- The rules of ORG.FR-7 apply, ignoring the period being edited.
- An edit that would leave any of the period's activities outside its new dates is refused. The message says how many activities would fall outside, so the user can move or delete them first (M2).
- An accepted edit never moves an activity to another period and never changes an emission figure.

## 5. Non-goals

The master PRD's non-goals (§2) and out-of-scope list (§10) apply. In addition, M1 does **not**:

- delete or archive organizations (FR-ORG-06);
- delete reporting periods. A wrong period is corrected by editing it (ORG.FR-8);
- move activities between periods automatically when periods change;
- limit who can create or change organizations: roles are labels only (A-01);
- isolate organizations from each other: every demo account sees every organization (A-02).

## 6. MVP scope

**MVP:** ORG.FR-1 to ORG.FR-4 and ORG.FR-6 to ORG.FR-8.
**Should:** ORG.FR-5 (headline figures).

Without ORG.FR-5, the details page shows the profile and periods only.

## 7. What M1 provides to other modules

| Module | Receives from M1 | Note |
|---|---|---|
| M0 Platform | The organizations shown in the switcher and the industry list served as reference data | The seed's demo organizations and periods follow M1's rules (PLT.FR-11) |
| M2 Activities | The periods that each activity's date is matched against | ORG.FR-8 guarantees that no period edit leaves an activity outside its period |
| M4 Dashboard, M5 Reports | The organization's periods, for their period selectors | Same newest-first order as ORG.FR-4 |
| M3 Emissions | Nothing directly | M1 uses M3's overall total for its headline figures |

## 8. Success metrics

- **SM-1: One-form onboarding.** A new organization, with its first period, is created in one form submission, after which its empty dashboard opens. Validates ORG.FR-1 and supports goal G1.
- **SM-2: Period integrity.** After any sequence of period adds and edits, no two periods of an organization overlap and no activity lies outside its period. Validates ORG.FR-7 and ORG.FR-8.
- **SM-C1 (counter-metric): Faster onboarding must not loosen period rules.** Do not create or stretch periods silently to make data entry easier. Counterbalances SM-1.

## 9. Open questions and assumptions

### Open questions

1. **Paging the organization list.** NFR-02 says list endpoints return at most 100 rows per page, but the organization list (ORG.FR-3) and the switcher (PLT.FR-2) need every organization. Should the organization list be exempt from paging, or should the switcher page or search? Owner: architect, before CP5. Not a blocker: the demo has two organizations.

### Assumptions index

All accepted at the draft review on 2026-11-15.

| # | Where | Assumption |
|---|---|---|
| 1 | ORG.FR-1 | Organization names are trimmed before they are checked |
| 2 | ORG.FR-1 | The first period starts filled with the current calendar year, named by its year |
| 3 | ORG.FR-2 | The industry list has the 12 values listed |
| 4 | ORG.FR-3 | The organization list shows name, industry and country, sorted by name |
| 5 | ORG.FR-4 | Periods are listed newest first |
| 6 | ORG.FR-7 | Period names are trimmed and unique ignoring case |
| 7 | ORG.FR-7 | Periods may be in the future and have no maximum length |
