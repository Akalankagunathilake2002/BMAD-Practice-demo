# Addendum — Module PRD: Emissions Calculation (M3)

Detail that supports [prd.md](prd-emi-2026-11-14.md) but belongs in the architecture, UX or stories. The decisions themselves are recorded in [.memlog.md](.memlog.md).

## Options considered

| Topic | Chosen | Not chosen |
|---|---|---|
| Which factor an edit uses (EMI.FR-1) | **Always the active factor**, as FR-ACT-05 and architecture §5 already say | Recalculate only when quantity or type changes; keep the activity's old factor until an explicit recalculation |
| Source label when adding a version (EMI.FR-7) | **Editable source text, with the disclaimer always attached** | A fixed demonstration label with only the value entered |
| What recalculation shows (EMI.FR-9) | **Count of out-of-date calculations, confirm, then the number updated** | A plain confirmation dialog |
| Months with no data (EMI.FR-6) | **Left out of the response.** M4 handles gaps | Every month in the period returned, with 0 where empty |

The edit rule means that after a factor change, fixing a typo in an activity's description also changes its emissions figure. This was accepted because it matches the approved master documents. The earlier figure stays in the calculation history.

## Architecture impacts (for the architect, before CP5)

These are proposals. The architect decides the final shape.

1. **Out-of-date count (EMI.FR-9).** Architecture §9.2 has `POST /organizations/{orgId}/emissions/recalculate` but no read-only count. One option is `GET /organizations/{orgId}/emissions/recalculate` returning `{ outOfDateCount }`, with the `POST` returning `{ updatedCount }`.
2. **Source text (EMI.FR-7).** `POST /emission-factors` needs an optional `sourceText` field (up to 100 characters). The stored `source_label` (`VARCHAR(200)`) is then the source text followed by the standard disclaimer, or the standard label alone when the text is blank. The disclaimer must be added on the server, never by the client.
3. **Monthly aggregation (EMI.FR-6).** The `GROUP BY date_trunc('month', activity_date)` query in architecture §7 already leaves out months with no rows. No change is needed, but the API documentation should say so.
4. **Recalculation transaction (EMI.FR-9).** The architecture's one-transaction-per-request rule (§6.2) already makes recalculation all-or-nothing.
5. **When a factor version was added (EMI.FR-8).** Architecture §8.2 gives `created_at` only to mutable tables, and `emission_factors` rows are immutable, so there is no timestamp to show. Add a `created_at TIMESTAMPTZ NOT NULL` column to `emission_factors` and return it from `GET /emission-factors`.
