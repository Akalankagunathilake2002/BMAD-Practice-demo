---
title: "CarbonTrack — Module PRD: Activity Data Management (M2)"
status: final
created: 2026-11-15
updated: 2026-11-15
module: M2
slug: act
parent: docs/planning/prd.md
---

# CarbonTrack — Module PRD: Activity Data Management (M2)

| | |
|---|---|
| Module | M2 Activity Data Management, slug `act` |
| Status | Final, awaiting CP3 approval (merge of the CP3 pull request, Q-17) |
| Scope | 9 FRs: ACT.FR-1 to 7 MVP; ACT.FR-8 and 9 Should (§6) |
| Parent | [Master PRD v0.2](../../prd.md) (approved at CP1) |
| Constraints from | [Master Architecture v0.2](../../architecture.md) (approved at CP2) |
| Related | [M3 module PRD](../prd-emi-2026-11-14/prd.md) (calculation on save) · [M1 module PRD](../prd-org-2026-11-15/prd.md) (periods) · [M0 module PRD](../prd-plt-2026-11-14/prd.md) (page states) |
| Checkpoint | CP3 Module PRDs |
| Decision log | [.memlog.md](.memlog.md) · Supporting detail in [addendum.md](addendum.md) |

## 0. Document purpose

This PRD details what M2 must do so that UX (CP4) and epics and stories (CP5) can be written from it. It does not repeat the master PRD. Where a rule already exists there, in the architecture or in another module PRD, this document cites its ID (for example FR-ACT-02, A-05, EMI.FR-1, ORG.FR-8, PLT.FR-14) and adds only what is left open.

New functional requirements are numbered **ACT.FR-1** onward. Each carries a tag:

- **MVP**: refines a Must requirement in the master PRD, so it is part of the MVP (master §9).
- **Should**: refines a master requirement with that priority.

Rules inferred where the source documents are silent are marked `[ASSUMPTION]` and indexed in §9. All of them were accepted at the draft review on 2026-11-15. The tags stay so that story refinement can trace them.

All master non-functional requirements apply to M2 unchanged. NFR-02 (paginated lists), NFR-03 (tables scroll inside their container) and NFR-05 (field-level errors) matter most here.

## 1. Vision

M2 is where the data comes in. The Operations Coordinator who holds the utility bills, fuel-card statements and travel bookings enters each item as one activity: a type, a quantity and a date. M2 makes that fast and hard to get wrong. The unit follows the type, the period follows the date, and every mistake can be corrected by editing or deleting.

M2 never calculates anything itself. The moment an activity is saved, M3 produces its emission calculation (EMI.FR-1), and the row shows the result and the factor used. M2 owns the activity records and the Activities page; M3 owns the numbers.

## 2. Users and journeys

The personas are defined in [project brief §3](../../../analysis/project-brief.md#3-users-and-stakeholders). M2 is used mainly by the **Operations / Facilities Coordinator**, who enters activities quickly and fixes mistakes, and by the **Sustainability Manager**, who checks data quality in the list.

M2 realizes master journey **J2** (record monthly activity) in full. The activity list is also where a manager goes after a period edit is refused (ORG.FR-8), to move or delete the activities in the way.

## 3. Glossary

*Activity*, *activity type*, *reporting period* and *emission calculation* keep their master meanings, and *current calculation*, *calculation history* and *active factor* are defined in the M3 PRD. This PRD adds:

- **Activity record.** One saved activity: type, quantity, unit, activity date, optional description, organization and period.
- **Activity date.** The date the activity happened (A-05), called "reporting date" in the master PRD.
- **Activities page.** The page with the activity list and the add and edit forms.
- **Selected period.** The reporting period chosen in the page's period selector, shared with the dashboard and reports (architecture §10.2).

## 4. Features

### 4.1 Recording activities

Realizes J2. Refines FR-ACT-01, FR-ACT-02 and FR-ACT-03.

#### ACT.FR-1 · MVP: Add an activity

A user adds an activity for the active organization from the Activities page.

**Consequences (testable):**
- The form asks for type, quantity, activity date and an optional description. The unit is shown, not chosen: it follows the type (A-06).
- `[ASSUMPTION]` The form opens as a dialog over the list (architecture §10.2), with the activity date empty and no type preselected.
- On save, the activity and its first emission calculation are created together (EMI.FR-1). The new row shows its emissions and the factor used (ACT.FR-5).
- `[ASSUMPTION]` After saving, the dialog closes, the list shows the new row, and a confirmation message appears and disappears on its own. If the new activity falls outside the selected period, the confirmation says so and offers to switch to its period.
- The organization's dashboard and reports reflect the new activity the next time they load.

#### ACT.FR-2 · MVP: Validation

Refines FR-ACT-02. The server is the authority; the form only gives early feedback (architecture §12.3).

**Consequences (testable):**
- Quantity: more than 0, at most 1,000,000,000, at most 3 decimal places. `[ASSUMPTION]` Thousands separators typed by the user are accepted and removed.
- Activity date: not after today, and inside one of the organization's periods. `[ASSUMPTION]` "Today" is the server's calendar date; activity dates carry no time zone (A-11).
- Description: at most 500 characters.
- Every failed rule is shown next to its field, in the words of the error envelope's details (PLT.FR-13). A date outside every period says so and names the organization's periods, so the user can add one (ORG.FR-7) or fix the date.
- Unit and type must match. The form cannot produce a mismatch; the server still checks it (NFR-06).
- A save is refused when the activity type has no active factor (EMI.FR-1). The form shows the error at the type field.

#### ACT.FR-3 · MVP: Link to the reporting period

Refines FR-ACT-03 and A-04.

**Consequences (testable):**
- On save, the activity is linked to the one period of its organization that contains its activity date. The user never chooses the period.
- If the date later falls outside the period because the period was edited, the edit is refused by M1 (ORG.FR-8). An activity never ends up outside its period.

### 4.2 Reviewing activities

Refines FR-ACT-04.

#### ACT.FR-4 · MVP: Activity list

The Activities page lists the active organization's activities in a table.

**Consequences (testable):**
- The list opens on the **selected period**, the same selection the dashboard uses (FR-DSH-04). An "All periods" option shows every activity of the organization.
- Filters: activity type, reporting period and date range. Filters combine. `[ASSUMPTION]` A date range outside the selected period is allowed and overrides the period filter, so a user can look across periods without choosing "All periods".
- Sorting: `[ASSUMPTION]` by activity date (default, newest first), type, quantity and emissions. Clicking a column header sorts by it, clicking again reverses it.
- Pagination follows NFR-02: 25 rows per page by default, 100 at most, with the total count shown.
- Each row shows: activity date, type, quantity with unit, description, emissions in kg CO2e, the factor used (ACT.FR-5) and the actions Edit and Delete.
- The table scrolls inside its container on narrow screens (NFR-03). `[ASSUMPTION]` Which columns stay visible first on narrow screens is set in UX (CP4).
- The page uses the shared loading, error and empty states (PLT.FR-14). The empty state's main action is "Add activity". `[ASSUMPTION]` When filters hide every row but the organization has activities, the empty state says "No activities match these filters" with a "Clear filters" action instead.
- `[ASSUMPTION]` Filters and sort are kept in the page address, like the period, so a filtered list can be linked.

#### ACT.FR-5 · MVP: Show the calculation on each row

Refines FR-ACT-04, FR-EMI-03 and EMI.FR-3.

**Consequences (testable):**
- Each row shows the current calculation's emissions in kg CO2e to 2 decimals (master §6, Q-11) and the factor used: value, unit and version. The source label and the demonstration disclaimer are visible on the page (FR-EMI-09).
- `[ASSUMPTION]` The calculation history (EMI.FR-2) is reachable from the row, for example in the edit dialog or a details view, as decided in UX (CP4). It lists each calculation with its emissions, factor version and time, newest first.

### 4.3 Correcting activities

Refines FR-ACT-05 and FR-ACT-06.

#### ACT.FR-6 · MVP: Edit an activity

Refines FR-ACT-05.

**Consequences (testable):**
- Type, quantity, activity date and description can all be changed, under the rules of ACT.FR-2.
- Changing the type changes the unit with it. The quantity is kept as typed. `[ASSUMPTION]` The form points out that the unit has changed, so a fuel quantity in litres is not silently kept as kilowatt-hours.
- Every saved edit creates a new current calculation with the active factor, and the earlier one stays in the history (EMI.FR-1, EMI.FR-2). This includes edits that change only the description.
- If the new date falls in another period of the organization, the activity is **moved to that period automatically** (ACT.FR-3). If no period contains the new date, the edit is refused like any other validation error.
- `[ASSUMPTION]` If two people edit the same activity at the same time, the last save wins. No conflict detection in the MVP.

#### ACT.FR-7 · MVP: Open the list pre-filtered from a link

Supports ORG.FR-8. When M1 refuses a period edit because activities would fall outside the new dates, the user needs to reach exactly those activities.

**Consequences (testable):**
- The Activities page accepts a period and a date range in its address and opens with those filters applied (ACT.FR-4).
- `[ASSUMPTION]` M1's refusal message links to the Activities page with the period and the affected date range filled in. The user then edits the dates or deletes the activities (ACT.FR-6, ACT.FR-8) and retries the period edit.
- Nothing is moved or deleted by the link itself. The user makes each change deliberately.

#### ACT.FR-8 · Should: Delete an activity

Refines FR-ACT-06.

**Consequences (testable):**
- Delete asks for confirmation, naming the activity's date, type and quantity.
- On confirmation, the activity and its whole calculation history are removed (FR-ACT-06). Totals, the dashboard and reports no longer include it.
- There is no undo. `[ASSUMPTION]` The confirmation says so.

#### ACT.FR-9 · Should: Scope label on each row

Refines FR-EMI-11 and EMI.FR-10.

**Consequences (testable):**
- Each row shows its activity type's informational GHG scope label, taken from the reference data (PLT.FR-10), when EMI.FR-10 is built.

## 5. Non-goals

The master PRD's non-goals (§2) and out-of-scope list (§10) apply. In addition, M2 does **not**:

- import activities in bulk from CSV or Excel (FR-ACT-07);
- let the user choose an activity's period: it always follows the date (A-04);
- convert units or accept a unit other than the type's canonical one (A-06, Q-05);
- support fuel subtypes or travel modes (master §10);
- calculate or display totals: that is M3, M4 and M5;
- detect concurrent edits or offer undo.

## 6. MVP scope

**MVP:** ACT.FR-1 to ACT.FR-7.
**Should:** ACT.FR-8 (delete) and ACT.FR-9 (scope label).

Without ACT.FR-8, a wrong activity can only be corrected by editing it.

## 7. What M2 provides to other modules

| Module | Receives from M2 | Note |
|---|---|---|
| M3 Emissions | Each saved activity, for calculation; a deleted activity removes its history | M2 saves, M3 calculates, in one transaction (EMI.FR-1) |
| M1 Company | The filtered Activities page that a refused period edit links to (ACT.FR-7) | M1 refuses; M2 is where the user fixes it |
| M4 Dashboard, M5 Reports | Nothing directly | They read M3's aggregates and, for report line items, the activity records (FR-RPT-03) |
| M0 Platform | The validation rules that seeded demo activities must pass (PLT.FR-11) | |

## 8. Success metrics

- **SM-1: Fast entry.** An activity is added in one dialog with three required fields, with no period or unit to choose. Validates ACT.FR-1 and ACT.FR-3, and supports goal G1.
- **SM-2: Every row explainable.** Every row in the list shows its emissions and the factor used. Validates ACT.FR-5 and goal G2.
- **SM-3: Clean rejects.** Every invalid save is refused by the server with a field-level error, including saves that bypass the form. Validates ACT.FR-2.
- **SM-C1 (counter-metric): Fast entry must not relax validation.** Do not accept dates outside every period, or quantities of 0, to make entry smoother. Counterbalances SM-1.

## 9. Open questions and assumptions

### Open questions

None block M2.

### Assumptions index

All accepted at the draft review on 2026-11-15.

| # | Where | Assumption |
|---|---|---|
| 1 | ACT.FR-1 | The add form is a dialog over the list, with no type preselected |
| 2 | ACT.FR-1 | After saving, a confirmation appears; if the activity is outside the selected period, it offers to switch |
| 3 | ACT.FR-2 | Thousands separators in the quantity are accepted and removed |
| 4 | ACT.FR-2 | "Not in the future" uses the server's calendar date |
| 5 | ACT.FR-4 | A date-range filter may reach outside the selected period |
| 6 | ACT.FR-4 | Sortable columns: date (default newest first), type, quantity, emissions |
| 7 | ACT.FR-4 | Column priority on narrow screens is set in UX |
| 8 | ACT.FR-4 | A filtered-out list shows "No activities match these filters" with "Clear filters" |
| 9 | ACT.FR-4 | Filters and sort are kept in the page address |
| 10 | ACT.FR-5 | The calculation history is reachable from the row; UX decides where |
| 11 | ACT.FR-6 | The form points out when a type change changed the unit |
| 12 | ACT.FR-6 | Concurrent edits: last save wins |
| 13 | ACT.FR-7 | M1's refusal message links to the pre-filtered Activities page |
| 14 | ACT.FR-8 | The delete confirmation says there is no undo |
