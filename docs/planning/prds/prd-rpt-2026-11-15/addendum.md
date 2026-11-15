# Addendum — Module PRD: Reports (M5)

Detail that supports [prd.md](prd.md) but belongs in the architecture, UX or stories. The decisions themselves are recorded in [.memlog.md](.memlog.md).

## Options considered

| Topic | Chosen | Not chosen |
|---|---|---|
| Months with no activity (RPT.FR-2) | **Listed with 0**, same months as the dashboard | Only months with data |
| CSV number precision (RPT.FR-4) | **Full stored precision** (6 decimals for emissions and factors) | 2 decimals, as on screen |
| Summary CSV layout (RPT.FR-5) | **Month rows × type columns, plus a Total row** | Long format (month, type, value); two tables in one file |
| Type filter scope (RPT.FR-1) | **Applies to everything** | Line items only; summary always shows all types |

**Why full precision.** With 2 decimals, summing a few hundred rounded rows in a spreadsheet can drift from the on-screen total by a few hundredths of a kilogram. That is exactly the kind of mismatch that makes a finance reader distrust the file. Six decimals is what is stored, so the sum is exact. The screen keeps 2 decimals for readability.

## CSV columns (proposal for the story)

**Line items CSV** (RPT.FR-4), in this order:

`activity_date, activity_type, quantity, unit, emissions_kg_co2e, factor_value, factor_unit, factor_version, factor_source, description`

`factor_source` is the source label and carries the disclaimer (Q-10). With RPT.FR-7, `scope` is added after `activity_type`.

**Summary CSV** (RPT.FR-5):

`month, electricity_kg_co2e, fuel_kg_co2e, business_travel_kg_co2e, total_kg_co2e, source`

`month` is `YYYY-MM`. The last row has `Total` in the `month` column.

**File name pattern** (assumption 4):

`carbontrack_<organization>_<period>_<type or all-types>_<lines|summary>_<YYYY-MM-DD>.csv`

The organization and period names are lower-cased, with spaces and unsafe characters replaced by `-`.

## Architecture notes

- **Filling months (RPT.FR-2, RPT.FR-5).** The summary CSV is built on the server, so `ReportService` must fill the months of the period with zeros from the period's start and end dates; EMI.FR-6 leaves them out. For consistency the on-screen summary should come from the same filled data. The dashboard fills months on the client (DSH.FR-3); both must produce the same months.
- **Precision (RPT.FR-4).** The CSV is written from `Decimal` values on the server, so 6-decimal output is exact. ADR-09's "decimals as JSON numbers" applies to the JSON API only, not to the CSV.
- **Formula-injection guard (assumption 6).** Applied when writing the CSV, only to text columns (`description`, `factor_source`). Numeric columns are never prefixed.
- **Large exports (assumption 7).** Stream the CSV rows from the query instead of building the whole file in memory.
- **Endpoints.** The existing report and export endpoints in architecture §9.2 already take `periodId`, `activityType` and `part=lines|summary`. No new endpoint is needed.
- **Type filter on aggregates (RPT.FR-1, RPT.FR-2).** The M3 summary and monthly queries take a period but no activity type. `EmissionsQueryService` needs an optional activity-type parameter so the report summary can be filtered by type.
- **One call or two (RPT.FR-6).** `GET …/reports/emissions` returns the summary and the line items together. Paging through line items would then reload the summary each time. The architect decides whether to split it into a summary call and a paginated line-items call.
