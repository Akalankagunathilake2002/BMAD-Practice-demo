# Addendum — Module PRD: Dashboard (M4)

Detail that supports [prd.md](prd-dsh-2026-11-15.md) but belongs in the architecture, UX or stories. The decisions themselves are recorded in [.memlog.md](.memlog.md).

## Options considered

| Topic | Chosen | Not chosen |
|---|---|---|
| Months M3 leaves out (DSH.FR-3) | **Show as zero across the whole period**, future months included | Leave them out; show as zero only up to the current month |
| Previous period (DSH.FR-8) | **The period ending most recently before the selected one starts** | A same-length date range ending the day before; dropping FR-DSH-07 |
| Empty period (DSH.FR-5) | **Cards show 0, chart area shows the empty state** | The whole page becomes one empty state |

**Why future months as zero.** A period that has not ended, such as the current calendar year, shows its full shape with the months still to come empty. That makes "we are in November" obvious at a glance and keeps the chart the same width all year. The cost: a demo viewer might read a future zero as missing data. The month labels and the current date make this clear enough for a demo.

**Why the period before, not a same-length range.** It uses the organization's own periods, which is what the user already understands, and it needs no extra aggregate query. Periods of different lengths are a known weakness, accepted for a Could requirement.

## Architecture notes

- **Filling months (DSH.FR-3).** The frontend fills the gap months with zero from the period's start and end dates (`reporting_periods`), so no API change is needed. EMI.FR-6 stays as it is.
- **Comparison data (DSH.FR-8).** Two calls to the summary endpoint with different `periodId` values; the frontend works out the previous period from the period list. No new endpoint.
- **Independent loading (assumption 7).** The summary and monthly endpoints are already separate queries; the TanStack Query hooks keep them separate (architecture §10.1).
- **Chart table equivalent (DSH.FR-6).** Architecture §10.1 already assigns an "accessible table equivalent" to `components/charts/`, which §11.1 places in M4.
- **"Add activity data" link (DSH.FR-5).** The empty state should open the Activities page with the add dialog already open (ACT.FR-1). The route `/organizations/:orgId/activities` needs a parameter for this, for example `?add=1`.

## UX notes (for CP4)

- Card layout and what fits without scrolling at 1366 px (SM-1).
- Axis unit switching for small totals (assumption 4).
- Screen-reader chart summaries (assumption 8).
- Tooltip and legend behaviour on touch screens (DSH.FR-7).
- Favourable and unfavourable markers for the comparison (assumption 10).
