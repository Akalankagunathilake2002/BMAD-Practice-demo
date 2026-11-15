---
title: "CarbonTrack — Module PRD: Reports (M5)"
status: final
created: 2026-11-15
updated: 2026-11-15
module: M5
slug: rpt
parent: docs/planning/prd.md
---

# CarbonTrack — Module PRD: Reports (M5)

| | |
|---|---|
| Module | M5 Reports, slug `rpt` |
| Status | Final, awaiting CP3 approval (merge of the CP3 pull request, Q-17) |
| Scope | 7 FRs: RPT.FR-1 to 4 and 6 MVP; RPT.FR-5 Should; RPT.FR-7 Could (§6) |
| Parent | [Master PRD v0.2](../../prd.md) (approved at CP1) |
| Constraints from | [Master Architecture v0.2](../../architecture.md) (approved at CP2) |
| Related | [M3 module PRD](../prd-emi-2026-11-14/prd.md) (aggregates, source label) · [M4 module PRD](../prd-dsh-2026-11-15/prd.md) (shared period, months as zero) · [M2 module PRD](../prd-act-2026-11-15/prd.md) (activity records) · [M0 module PRD](../prd-plt-2026-11-14/prd.md) (page states) |
| Checkpoint | CP3 Module PRDs |
| Decision log | [.memlog.md](.memlog.md) · Supporting detail in [addendum.md](addendum.md) |

## 0. Document purpose

This PRD details what M5 must do so that UX (CP4) and epics and stories (CP5) can be written from it. It does not repeat the master PRD. Where a rule already exists there, in the architecture or in another module PRD, this document cites its ID (for example FR-RPT-04, Q-10, EMI.FR-4, DSH.FR-3, PLT.FR-14) and adds only what is left open.

New functional requirements are numbered **RPT.FR-1** onward. Each carries a tag:

- **MVP**: refines a Must requirement or a master NFR, so it is part of the MVP (master §9).
- **Should** or **Could**: refines a master requirement with that priority.

Rules inferred where the source documents are silent are marked `[ASSUMPTION]` and indexed in §9. All of them were accepted at the draft review on 2026-11-15. The tags stay so that story refinement can trace them.

All master non-functional requirements apply to M5 unchanged. NFR-01 (exact arithmetic), NFR-02 (aggregate speed and pagination) and NFR-06 (safe handling of user input) matter most here.

## 1. Vision

Reports are where CarbonTrack's numbers leave the app. A Sustainability Manager picks a period, checks the summary and the line items behind it, and exports CSV files that a finance colleague can open, sum and trust. The test of M5 is simple: add up the exported rows in a spreadsheet and you get exactly the total on screen, with every row showing the factor that produced it and the reminder that the factors are demonstration values.

M5 calculates nothing. Totals come from M3 (EMI.FR-5, EMI.FR-6) and line items from M2's activity records with their current calculations, so the report, the dashboard and the organization's headline figures always agree.

## 2. Users and journeys

The personas are defined in [project brief §3](../../../analysis/project-brief.md#3-users-and-stakeholders). M5 serves the **Sustainability Manager**, who produces reports, and the **Executive / Finance Reviewer**, who needs "a CSV to use in other tools".

M5 realizes the second half of master journey **J3**: Reports → filter by period and type → review line items → export CSV.

## 3. Glossary

*Reporting period*, *activity type*, *current calculation* and *source label* keep their meanings from the master PRD and the M3 PRD. *Selected period* is defined in the M2 PRD. This PRD adds:

- **Report.** What the Reports page shows for one organization, one reporting period and an optional activity type: a summary and its line items.
- **Report filters.** The reporting period (required) and the activity type (optional) that define a report.
- **Summary.** The report's total, its breakdown by activity type and its breakdown by month.
- **Line item.** One activity in the report, with its current calculation and the factor version used.
- **Line items CSV.** The export of every line item matching the report filters (FR-RPT-04).
- **Summary CSV.** The export of the summary by month and type (FR-RPT-05).

## 4. Features

### 4.1 Choosing the report

Refines FR-RPT-02.

#### RPT.FR-1 · MVP: Report filters

**Consequences (testable):**
- The reporting period is required. It defaults to the selected period shared with the dashboard and the Activities page (DSH.FR-1), and the period selector lists periods in the same order (ORG.FR-4).
- The activity type is optional; the default is all types.
- The type filter applies to **everything**: the summary, the line items and both exports. When a type is chosen, the page states it clearly (for example "Fuel only"), and so do the export file names.
- Both filters are part of the page address, so a report can be linked and bookmarked (architecture §10.2).
- Switching organization resets the period to the new organization's default (PLT.FR-2). The type filter is kept.

### 4.2 Summary

Refines FR-RPT-01.

#### RPT.FR-2 · MVP: Period summary

**Consequences (testable):**
- The summary shows the total, the breakdown by activity type with each type's share, and the breakdown by month with each type within the month.
- Figures come from M3 (EMI.FR-5, EMI.FR-6) and use current calculations only. For the same filters, the total equals the dashboard total (DSH.FR-2).
- Without a type filter, all three activity types are listed, with 0 where a type has no activity (EMI.FR-5). With a type filter, only that type is listed.
- The month breakdown lists **every month of the period**, with 0 for months with no activity, the same months as the dashboard trend (DSH.FR-3). For a period that has not ended, this includes its future months.
- Values are shown in kg CO2e to 2 decimals, as for all tables (master §6, Q-11). `[ASSUMPTION]` Shares are whole percentages, as on the dashboard.
- `[ASSUMPTION]` The summary is shown as tables only. Charts belong on the dashboard.

### 4.3 Line items

Refines FR-RPT-03.

#### RPT.FR-3 · MVP: Line items

**Consequences (testable):**
- The line items are the activities matching the report filters, each with the columns listed in FR-RPT-03, plus the factor version.
- Line items are read-only. Corrections are made on the Activities page (ACT.FR-6).
- `[ASSUMPTION]` Line items are sorted by activity date, oldest first, as a report reads chronologically.
- The list is paginated under NFR-02 and shows the total number of line items.
- Emissions are shown in kg CO2e to 2 decimals (Q-11). The demonstration disclaimer is visible on the page (FR-EMI-09).

### 4.4 Exports

Refines FR-RPT-04 and FR-RPT-05.

#### RPT.FR-4 · MVP: Line items CSV

**Consequences (testable):**
- The file contains **every** line item matching the report filters, not just the page on screen.
- It meets FR-RPT-04: UTF-8, one header row, and each row's factor value, factor version and source label. The source label already contains the demonstration disclaimer (EMI.FR-4, Q-10).
- Numbers are written at **full stored precision**: emissions and factor values to 6 decimals, quantities to 3. They use a dot as the decimal separator and no thousands separators. Summing the emissions column gives exactly the report total (NFR-01).
- Dates are written as `YYYY-MM-DD` (architecture §9.1).
- `[ASSUMPTION]` The file name names the organization, the period, the type filter, the content and the export date. The pattern is in the addendum.
- `[ASSUMPTION]` The file starts with a UTF-8 byte-order mark so spreadsheet programs show non-English characters correctly.
- `[ASSUMPTION]` A description that starts with `=`, `+`, `-` or `@` is written with a leading apostrophe, so a spreadsheet does not run it as a formula (NFR-06).
- `[ASSUMPTION]` Exporting 50,000 line items finishes within 10 seconds on a developer laptop.

#### RPT.FR-5 · Should: Summary CSV

**Consequences (testable):**
- One file, one header row: one row per month of the period, one column per activity type and a total column, then a final "Total" row with each type's total and the overall total.
- Every month of the period has a row, with 0 where there is no activity (RPT.FR-2).
- With a type filter, only that type's column and the total column appear.
- Numbers use the same full precision and format as the line items CSV (RPT.FR-4). The "Total" row equals the report total.
- `[ASSUMPTION]` Because this file has no factor column, every row carries a "source" column holding the standard demonstration label, so the disclaimer travels with the file (FR-EMI-09).
- The file name follows the same pattern as RPT.FR-4, with "summary" as the content.

### 4.5 Page states

#### RPT.FR-6 · MVP: Page states

Refines G3, FR-EMI-09 and PLT.FR-14.

**Consequences (testable):**
- While loading, the summary and the line items show skeletons (PLT.FR-14).
- When the period has no activity matching the filters, the summary shows zeros and the line items show the empty state. `[ASSUMPTION]` Its main action is "Add activity", which opens the Activities page with the add form (ACT.FR-1).
- When the organization has no periods, the page shows an empty state that leads to adding a period (ORG.FR-7).
- `[ASSUMPTION]` Export actions are disabled when there are no line items, with a short reason.
- A failed load or export shows the error panel or message with Retry (PLT.FR-13). A failed export never produces a partial file.

### 4.6 Scope labels

#### RPT.FR-7 · Could: Scope label in the report

Refines FR-EMI-11 and EMI.FR-10.

**Consequences (testable):**
- When EMI.FR-10 is built, each line item and the type breakdown show the activity type's informational scope label, and the line items CSV has a scope column.

## 5. Non-goals

The master PRD's non-goals (§2) and out-of-scope list (§10) apply. In addition, M5 does **not**:

- export PDF, or schedule or email reports (FR-RPT-06);
- report across several periods, organizations or arbitrary date ranges;
- edit data: line items are read-only;
- calculate anything: every number comes from M3;
- claim compliance with any reporting framework (master §2).

## 6. MVP scope

**MVP:** RPT.FR-1 to RPT.FR-4 and RPT.FR-6.
**Should:** RPT.FR-5 (summary CSV).
**Could:** RPT.FR-7 (scope labels).

Without RPT.FR-5, the summary is available on screen and through the line items CSV, which can be summed.

## 7. What M5 provides to other modules

| Module | Receives from M5 | Note |
|---|---|---|
| None | M5 is the end of the chain | It reads M3 aggregates, M2 activity records and M1 periods, and uses M0's shell and page states |

M5's month breakdown and M4's trend chart fill missing months the same way, so they always show the same months for the same period.

## 8. Success metrics

- **SM-1: Spreadsheet check.** For any report, summing the emissions column of the line items CSV gives exactly the report total, and the summary CSV's Total row matches it. Validates RPT.FR-2, RPT.FR-4 and RPT.FR-5, and NFR-01.
- **SM-2: One source of numbers.** The report total equals the dashboard total for the same period and type (DSH.FR-2). Validates RPT.FR-2 and goal G2.
- **SM-3: Opens cleanly.** Both CSV files open in Microsoft Excel, LibreOffice Calc and Google Sheets with the right columns, numbers and characters, without import settings. Validates RPT.FR-4 and RPT.FR-5.
- **SM-C1 (counter-metric): A cleaner file must not lose the disclaimer.** Do not drop the source label or source column to make the CSV look tidier. Counterbalances SM-3.

## 9. Open questions and assumptions

### Open questions

None block M5.

### Assumptions index

All accepted at the draft review on 2026-11-15.

| # | Where | Assumption |
|---|---|---|
| 1 | RPT.FR-2 | Shares are whole percentages, as on the dashboard |
| 2 | RPT.FR-2 | The summary is shown as tables only, no charts |
| 3 | RPT.FR-3 | Line items are sorted oldest first |
| 4 | RPT.FR-4 | The file name names organization, period, type filter, content and export date |
| 5 | RPT.FR-4 | The CSV starts with a UTF-8 byte-order mark |
| 6 | RPT.FR-4 | Descriptions starting with `=`, `+`, `-` or `@` get a leading apostrophe |
| 7 | RPT.FR-4 | Exporting 50,000 line items takes at most 10 seconds |
| 8 | RPT.FR-5 | The summary CSV carries the disclaimer in a "source" column on every row |
| 9 | RPT.FR-6 | The empty line items state offers "Add activity" |
| 10 | RPT.FR-6 | Export actions are disabled when there are no line items |
