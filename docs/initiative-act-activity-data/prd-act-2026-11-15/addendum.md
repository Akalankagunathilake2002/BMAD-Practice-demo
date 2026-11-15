# Addendum — Module PRD: Activity Data Management (M2)

Detail that supports [prd.md](prd-act-2026-11-15.md) but belongs in the architecture, UX or stories. The decisions themselves are recorded in [.memlog.md](.memlog.md).

## Options considered

| Topic | Chosen | Not chosen |
|---|---|---|
| Default list scope (ACT.FR-4) | **The selected period**, shared with the dashboard | All periods, newest first |
| Changing type on edit (ACT.FR-6) | **Allowed**; unit follows, emissions recalculated | Type fixed after creation |
| Date edit that crosses a period (ACT.FR-6) | **Re-link automatically** to the covering period | Refuse; delete and re-add instead |

**Why re-link rather than refuse.** The master already says the period follows the date (FR-ACT-03). Making the user delete and re-add would lose the calculation history for no gain. The trade-off: an activity can leave one period's totals and enter another's without a warning beyond the normal save confirmation. The confirmation in assumption 2 covers this.

**Type change and quantity.** Keeping the quantity as typed is deliberate: there is no conversion between kWh, litres and km (A-06), so the only honest options are to keep the number or clear it. Keeping it, with a visible note that the unit changed, is less work for the common case (wrong type picked, right number typed).

## Architecture notes

- **Date-range filter across periods (assumption 5).** `GET /organizations/{orgId}/activities` takes both `periodId` and a date range (architecture §9.2). The PRD expects an explicit date range to take precedence when both are given. The API documentation should state this.
- **Filtered list link from M1 (ACT.FR-7).** The route `/organizations/:orgId/activities` needs to accept the filters as query parameters (assumption 9). The period-edit error from ORG.FR-8 should carry the period ID and the offending date range so the UI can build that link.
- **Sort fields (assumption 6).** `?sort=` must accept `activityDate`, `activityType`, `quantity` and `emissionsKgCo2e`, each with a `-` prefix for descending. Sorting by emissions needs a join to the current calculation.
- **Re-link on date edit (ACT.FR-6).** `ActivityService.update` must look up the covering period again whenever the date changes, exactly as on create (architecture §5).
- **Last save wins (assumption 12).** No `updated_at` check on PATCH. Note it as a known limitation in architecture §16 if the demo ever has two users entering data at once.

## UX notes (for CP4)

- Where the calculation history appears (assumption 10). The M3 PRD deferred this to UX too.
- Column priority on narrow screens (assumption 7).
- The wording of the "unit changed" note when the type changes (assumption 11).
