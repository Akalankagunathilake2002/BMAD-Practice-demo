# Adversarial review: CarbonTrack architecture spine

| | |
| --- | --- |
| Reviewed | `architecture-carbontrack.md` (spine, status draft, 2026-11-15) |
| Read for context | parent `initiative-carbontrack.md`, `tickets/tickets.toml`, the six child initiatives, their PRDs and addenda (the builders' companions), `architecture-carbontrack-detail.md` |
| Lens | Find two child initiatives (M0–M5), built in tickets.toml order by builders who read only the spine and their own companions, that each obey every AD to the letter and still build incompatibly. Then run the good-spine checklist. |
| Date | 2026-11-15 |

**Verdict:** The spine is not yet safe to hand to six independent builders. It contradicts itself on who writes `activity_records`, and it leaves undefined the seams that Decisions A and B opened: cross-module code edits, the seed with three owners, gap-filling in two places, the decimal wire format, the client cache and the selected period. M0–M5 can each obey every AD and still build incompatibly, so one critical and six high findings need new or tightened ADs before epics are sliced.

## Findings at a glance

| ID | Severity | Units that clash | Spine location |
| --- | --- | --- | --- |
| F1 | critical | M3 and M2: two write paths for `activity_records` | AD-2, AD-3, AD-8, AD-15 |
| F2 | high | M0, M3 and M2: who commits, and the seed's transaction | AD-1 vs AD-4 |
| F3 | high | M4 and M5: month gap-filling, plus M3's month key | AD-9 |
| F4 | high | M0, M3 and M2: the seed's existence key, step order and gating | AD-15 |
| F5 | high | M1 and M3, M1 and M2, M0 and M1/M3: deliveries into code another module owns | AD-2, AD-3, Capability map |
| F6 | high | M3 and M2/M5 vs M4: decimal serialization and the meaning of share | AD-5, AD-9 |
| F7 | high | M2/M3 vs M4/M5/M1: TanStack Query invalidation | AD-14 |
| F8 | medium | M1 and M2: period-edit guard error shape, link parameters and race | AD-8, AD-3, AD-14, Deferred |
| F9 | medium | M1 vs M4/M2/M5, M2 vs M5, M0 pager: paging envelope, unpaged lists, sort, parameters | AD-10 |
| F10 | medium | M3 vs M2 vs M0: error details shape, field naming, status mapping | AD-10, Conventions |
| F11 | medium | M0 vs M1, M3 vs M2/M4/M5: where reference lists live and enum wire values | AD-15 |
| F12 | medium | M3 vs M5 vs M2/M4: the disclaimer string | AD-6 |
| F13 | medium | M2 vs M3: numeric ranges overflow storage and the JSON float | AD-5 |
| F14 | medium | M0 vs M5: authenticated, never-partial CSV download | AD-1, AD-11, AD-13 |
| F15 | medium | M0, M2, M4 and M5: default period, "All periods" encoding, propagation | AD-14 |
| F16 | medium | M3 vs M2: deleting an activity vs calculation history that is "never edited" | AD-7, AD-3 |
| F17 | low | M1 (intra-module), M5 vs M2: AD-3's wording and its read-anything clause | AD-3 |
| F18 | low | M1 vs M3, M3 vs M2: schema prerequisites across owners' migrations | AD-2, AD-8 |
| F19 | low | M2 server vs M2/M4 client: "today" | Conventions |
| F20 | low | Checklist (operations): startup failure, migration chain, reset, perf dataset, CORS origin | Structural Seed, AD-16, Deferred |
| F21 | low | M2/M4/M5: colour tokens per activity type (a Deferred item lets them diverge) | Deferred, AD-14 |
| F22 | low | M4/M5 vs M2: which route honours `?add=1` | AD-14 |
| F23 | low | M2 vs M5 (vs M3's SM-2 test): the shape of the calculation and factor in responses | AD-7, Conventions |

---

## Critical

### F1: `activity_records` has two legitimate write paths

- **Units:** M3, Emissions (built 3rd), and M2, Activity data (built 4th).
- **How they clash while obeying the ADs:**
  - **The M3 builder** reads in AD-2 that "only the owner's services write [its tables]", and M3 owns `activity_records`. M3's R11 also requires stored activities, linked to periods, to test the calculation core before M2 exists. So M3 builds `ActivityRecordService.create/update/delete` (or `EmissionCalculationService.save_activity`). AD-8 requires the server to link the period, so this writer derives it by reading `reporting_periods` through M1's model. AD-3's read clause allows that, because AD-3 gives M3 no call to `ReportingPeriodService`. The writer then raises its own error when no period covers the date.
  - **The M2 builder** reads AD-2's last sentence ("M2 owns the activity service … that create, edit and delete activity records"). AD-3 allows exactly one M2→M3 call: `EmissionCalculationService`, "on every save". M2 therefore cannot call M3's writer. `ActivityService` does `session.add(ActivityRecord(...))` itself and derives the period through `ReportingPeriodService`.
  - **The result:** two insert/update paths for one table, two covering-period lookups (for example `ACTIVITY_DATE_OUTSIDE_PERIODS` 422 at `activityDate` in one, and a `REPORTING_PERIOD_NOT_FOUND` 404 in the other), and two places that enforce AD-8.
  - **The seed** says it writes "through the owning module's services". Read literally, the owner is M3, so M2's demo activities go through M3's writer and skip M2's validation: quantity at most 1e9 with 3 decimals, no future dates, description at most 500 characters. That is the divergence AD-15 claims to prevent.
  - **Delete** is not a "save", so AD-3 gives M2 no M3 call for it either (see F16).
- **Spine location:** AD-2 Rule (its second and third sentences contradict each other), AD-3 Rule (the list of allowed calls), AD-8 Rule ("The server links…" names no service), AD-15 Rule ("through the owning module's services").
- **Fix (rewrite AD-2's Rule):**
  > Schema owners (migrations, models, constraints, indexes): M0 `users`; M1 `organizations`, `reporting_periods`; M3 `activity_records`, `emission_factors`, `emission_calculations`. Row writers: each table's schema owner, with one exception: rows of `activity_records` are inserted, updated and deleted only by `ActivityService` (M2). `EmissionCalculationService` writes only `emission_calculations`; `EmissionFactorService` writes only `emission_factors`. M3 builds no activity write path in `app/`. Its tests, and the R11 check, create activity rows through a test-only factory in `backend/tests/factories.py` that app code never imports.

  **Append to AD-8:**
  > The covering period is found only by `ReportingPeriodService.find_covering(org_id, date) -> ReportingPeriod | None` (M1). `ActivityService` calls it on create and on every date change, and raises `ACTIVITY_DATE_OUTSIDE_PERIODS` (422, field `activityDate`) when it returns `None`.

  **AD-15:** say that M2's demo activities go through `ActivityService.create`.

---

## High

### F2: AD-1 and AD-4 disagree on who commits, and the seed has no transaction rule

- **Units:** M0 (the `get_db` dependency and the pytest fixture, built 1st), M3 (factor add, calculate, recalculate) and M2 (`ActivityService`). M1 is involved too.
- **How they clash while obeying the ADs:**
  - **AD-1 says** "A service holds rules, queries and the transaction". **AD-4 says** "Each request runs in one database session that commits once at the end". The detail doc §6.2 adds "a write service method commits once at the end of its work".
  - **The M3 builder** follows AD-1 and the detail doc and commits inside `calculate_for_activity` and `add_version`. When M2's `ActivityService.update` calls the calculation and then fails (for example on a second check or while building the response), the activity and its calculation are already committed: a half-applied request.
  - **The M0 builder** follows AD-4 and commits in `get_db` after the route returns. The M1 builder, meanwhile, relies on catching `IntegrityError` from the overlap exclusion constraint and the `lower(name)` index inside the service, so it can name the clashing period (AD-8). Under M0's design the violation only surfaces at commit, outside the service, and becomes `500 INTERNAL_ERROR`. The same applies to M3 R7's "conflict error" for a simultaneous factor add.
  - **The seed** is not a request, so AD-4 does not bind it. With per-service commits, a failure halfway through M2's demo data leaves a half-seeded organization. AD-15's "changes nothing that already exists" then means no later start repairs it, and the NFR-01 golden totals stay wrong.
  - **Tests:** the Conventions table's "rolled-back transactions" pattern only works if services never commit, or if M0's fixture joins through savepoints. M0 builds that fixture before any write service exists.
- **Spine location:** AD-1 Rule ("holds … the transaction"), AD-4 Rule, the Conventions Testing row, AD-15.
- **Fix (replace AD-4's Rule):**
  > The unit of work belongs to the entry point, never to a service. For HTTP, M0's `get_db` opens one session per request, commits once after the route returns successfully, and rolls back on any exception before the error envelope is sent. For the seed, M0's runner opens one session per seed step (F4) and commits once per step. Services never call `commit()` or `rollback()`. After any write that a database constraint guards, they call `flush()`, catch `IntegrityError` there and raise the domain error. An activity write and its calculation, and a whole recalculation, therefore commit together or not at all. Tests wrap each test in an outer transaction with `join_transaction_mode="create_savepoint"`.

  In AD-1, change "holds rules, queries and the transaction" to "holds rules and queries and flushes; it never commits (AD-4)".

### F3: Months are gap-filled twice, with no algorithm and no month key

- **Units:** M4, Dashboard (fills on the client), and M5, Reports (fills on the server). M3 defines the monthly shape.
- **How they clash while obeying the ADs:** AD-9 states an outcome ("so both show the same months"), not a rule.
  - **The M5 builder** (Python) steps from `start_date.replace(day=1)` while the month start is on or before `end_date`, so it includes a partial last month.
  - **The M4 builder** (TypeScript) loops `for (d = new Date(start); d <= new Date(end); d.setMonth(d.getMonth()+1))`. For a period of 2025-04-15 to 2026-04-14 it stops at 2026-03-15 and drops April 2026. Activities dated 1–14 April are then in the card total but in no bar. `new Date('YYYY-MM-DD')` parses as UTC, so for users west of UTC every month shifts back by one. A period starting on the 31st skips February when `setMonth` overflows.
  - **Month key:** M3's natural query, `date_trunc('month', activity_date)`, casts the DATE to `timestamptz` and returns `"2025-05-01T00:00:00+00:00"`. M4 keys its filled months `"2025-05"`, the merge finds no match, and every bar is zero. M5's summary CSV wants `YYYY-MM` (M5 addendum).
  - **Type filter:** when M5 filters by type, the spine does not say whether the monthly `byType` still carries all three types or only the filtered one.
- **Spine location:** AD-9 Rule, last sentence.
- **Fix (replace AD-9's last sentence):**
  > `EmissionsQueryService` returns each month as a `YYYY-MM` string built with `to_char(activity_date, 'YYYY-MM')`, never through a timestamp. A period's months are every calendar month from the month of `start_date` to the month of `end_date` inclusive. Only the pure `period_months(start, end)` in `app/calculation/periods.py` (M3) computes them. Gap-filling happens once, on the server: `GET …/emissions/monthly` and the method `ReportService` calls both return every month of the period with zeros, and the client never fills months. Each month carries all three types; with a type filter, the other two are 0.

  If the M3 PRD's "months left out" choice must stand, the alternative is to keep the client fill but make both M4 and M5 tests pass one shared set of example periods: mid-month start, a start on the 31st, a year boundary, and a one-day period. Month arithmetic must then work on `(year, month)` integers, never on `Date` objects.

### F4: The seed has no existence key, no step order, and no rule for which steps the demo-data flag gates

- **Units:** M0 (the command and demo users), M3 (factor version 1) and M2 (demo organizations, periods and activities).
- **How they clash while obeying the ADs:** "Changes nothing that already exists" never says what identifies "exists".
  - **The M2 builder** keys demo organizations by name. R15 lets a user rename "Northwind Logistics", and the next `docker compose up` (the seed runs on every start) creates a second Northwind with 18 months of activity.
  - **Per-activity keys** (organization, date, type, quantity) are another reading. Then a demo activity the user deleted (R22) comes back on restart, which breaks "keeps users' edits" (M0 R11).
  - **Gating:** the M0 builder writes the command before factors exist and may gate the whole command on `SEED_DEMO_DATA`, while AD-15 implies factors are always seeded. With the flag off, nothing seeds factors and every activity save is refused with `NO_ACTIVE_FACTOR`.
  - **Order:** M0 defines no registration or ordering. M2's step needs an active factor, but M2 can append its call ahead of M3's.
  - **Transaction:** see F2.
- **Spine location:** AD-15 Rule.
- **Fix (replace AD-15's first four sentences):**
  > M0's `app/seed/__main__.py` runs an ordered registry of steps: `users` (M0), `factors` (M3), `demo_data` (M2). Each step is `run(session, settings) -> None` in its owner's `app/seed/<step>.py`, writes through the owning services, and commits once (AD-4). Existence keys are fixed:
  > - users: `lower(email)`
  > - factors: "this activity type has any version"
  > - demo data: a fixed UUID per demo organization. When that id exists, the step skips the organization and everything under it. Otherwise it creates the organization, its periods and all its activities in one transaction.
  >
  > Steps never update or recreate rows they find. The `factors` step always runs; `users` and `demo_data` run only when `SEED_DEMO_DATA` is true.

### F5: Decisions A and B route deliveries into code that another module owns, and nothing governs those edits

- **Units and how they clash:** AD-2 governs tables only. No AD says who may edit another module's service, schema, page or layout. Decision B, though, moves deliverables into modules that run *after* the code's owner. Every case below obeys the ADs, and each pair of builders produces a different structure.
  - **(a) Headline figures: M1 (2nd) and M3 (3rd).** M1 builds `GET /organizations/{orgId}` and the details page with no figures (M1 initiative: "built later, in M3"). For R13, the M3 builder either:
    - edits M1's `OrganizationService`, its response schema and its page, as AD-3's edge `OrganizationService → EmissionsQueryService` implies; or
    - adds `GET /organizations/{orgId}/emissions/summary` with no period and its own panel, which leaves the AD-3 edge unused and the block on M1's page unowned.

    If M1 shares one `OrganizationRead` between list and detail and M3 adds the figures to it, the unpaged `GET /organizations` behind the switcher runs one aggregate per organization. The Capability map row ("R14 … (M3, shown by M1)") does not settle who builds the UI.
  - **(b) The period-edit guard: M1 (2nd) and M2 (4th).** M1 builds period editing before `activity_records` exists (M3, 3rd). AD-3 puts the guard *in* `ReportingPeriodService`, but M2 delivers it (M2 R10). So the M2 builder edits M1's service and M1's period form, to show the count and link. Or the M2 builder puts the guard in `ActivityService` and calls it from `ReportingPeriodService`, which adds an M1→M2 edge that AD-3 does not list and that cycles with M2→M1.
  - **(c) Reference data: M0 (1st) vs M1 and M3.** M0 builds `GET /reference-data`. M1 adds industries and M3 adds scope labels (M0 R10), so both edit M0's service and schema (see F11).
  - **(d) The sidebar: M0 vs M3.** M0 hides the Emission Factors entry "while that page is not built" (M0 R1), and M3 must flip it in M0's layout.
  - **(e) The seed:** see F4.
  - **Dependencies:** AD-3 calls the graph acyclic, yet M1 depends on M3 (headline figures; the guard reads `activity_records`) and M3 depends on M1 (composite FK, period reads). The diagram shows only M3 → M0.
- **Spine location:** AD-2 (tables only), AD-3 Rule and diagram, Capability map row R14.
- **Fix (add AD-17, "Cross-module deliveries go through seams the owner leaves"):**
  > A module edits another module's code only at a seam named here. The owner creates the seam when it is built, and the later module fills only that seam.
  > - (a) M1's `OrganizationDetail` has `headline: HeadlineFigures | None`. `OrganizationService.get` fills it by calling `EmissionsQueryService.headline(org_id)` once M3 exists. M3 supplies `HeadlineFigures {activityCount, totalEmissionsKgCo2e}` and the `HeadlineFigures` component, which M1's details page renders in a reserved slot. `GET /organizations` never carries headline figures.
  > - (b) M1's `ReportingPeriodService.update` calls a private `_check_activities_inside(period, start, end)` that M1 ships as a no-op. M2 implements it as a read-only query on `activity_records`. M1's period form renders `REPORTING_PERIOD_EXCLUDES_ACTIVITIES` from day one, as F8 defines it.
  > - (c) M0's `ReferenceData` schema has `activityTypes`, `industries`, `countries` and `disclaimer` from the start, each filled from its owner's constant module (F11, F12).
  > - (d) M0's sidebar entries are a list in `layouts/navigation.ts` with an `enabled` flag that M3 sets.
  > - (e) The seed registry (F4).

  Redraw AD-3's diagram with the data-level edges M3 → M1 and M1 ⇢ M3, and label them read-only.

### F6: Neither the decimal wire format nor "share" has one definition

- **Units:** M3 (the first module with decimals in responses), and M2/M5 (later) as producers; M4 (TypeScript) as consumer. M1 is also affected through the headline figures.
- **How they clash while obeying the ADs:**
  - **Serialization:** AD-5 says "the JSON API sends decimals as numbers at fixed precision", but Pydantic v2 serializes `Decimal` as a JSON *string* by default, and no AD names who defines the serializer. The M3 builder declares `emissions_kg_co2e: Decimal` and ships `"6200.000000"`. The M2 builder adds its own `PlainSerializer(float)`. M4's types say `number`, so its own arithmetic for the R8 previous-period change concatenates strings, and `toFixed` throws on them.
  - **Share:** AD-9 says shares come from `EmissionsQueryService` in SQL, while AD-5 says rounding happens "only in the engine". The share is therefore either an unrounded NUMERIC quotient of about 20 digits, or it is rounded outside the engine in breach of AD-5. Its unit is not defined either. M3 returns the fraction 0.3456, M4 (whose PRD says "whole percentages") renders `Math.round(share)` as "0%", and M5 renders `share*100`, so the dashboard and the report show different shares for the same period. SM-2 checks only totals, so it does not catch this.
- **Spine location:** AD-5 Rule ("numbers at fixed precision", "only in the engine"), AD-9 Rule ("every … share … comes from EmissionsQueryService").
- **Fix (append to AD-5's Rule):**
  > M0's `app/schemas/common.py` defines the only decimal response types, and every module uses them: `Quantity` (3 dp), `FactorValue` (6 dp), `KgCo2e` (6 dp) and `Share` (a fraction from 0 to 1, 6 dp). Each serializes as a JSON number after `quantize(…, ROUND_HALF_UP)`. No response schema declares a bare `Decimal`. Shares are computed only by `share(part, total)` in `app/calculation`, which returns 0 when the total is 0; this is the engine's second and last rounding point. On the frontend, M0's `utils/format.ts` is the only formatter (`formatKg`, `formatTonnes`, `formatShare` from a fraction to a whole percent), and no page does arithmetic on API figures other than kg→t and R8's change.

### F7: Mutations cannot invalidate queries that do not exist yet

- **Units:** M2 (activity mutations, 4th) and M3 (factor add and recalculation, 3rd) vs M4 (dashboard queries, 5th), M5 (report queries, 6th) and M1 (organization detail with headline figures).
- **How they clash while obeying the ADs:** AD-14 says "mutations invalidate the queries they affect", but M2 and M3 are built before M4 and M5 define their query keys. M2 invalidates `['activities', orgId]`. M4 caches `['emissions-summary', orgId, periodId]`. M3's recalculation invalidates `['emissions']`. If M0's QueryClient has any non-zero `staleTime` (a common default), the dashboard, the headline figures and the report keep showing old totals after an activity is saved or a recalculation runs. M4 R5 ("a save elsewhere shows on the next view without a manual refresh") and SM-2 (one source of numbers) both fail, with every AD obeyed.
- **Spine location:** AD-14 Rule, last sentence.
- **Fix (replace AD-14's last sentence):**
  > Server state lives only in TanStack Query, under keys built by M0's `hooks/queryKeys.ts`. Every organization-scoped key starts with `['org', orgId]`, and global keys start with `['global', <resource>]`. A mutation that writes activities, periods or calculations invalidates `['org', orgId]`. Adding a factor version or running a recalculation invalidates every `['org']` key and `['global', 'emission-factors']`. Creating or editing an organization invalidates `['global', 'organizations']` and `['org', orgId]`.

---

## Medium

### F8: The period-edit guard has no error shape or link parameters, and races with activity saves

- **Units:** M1 (period form and `ReportingPeriodService.update`) and M2 (the guard, and the Activities page that the refusal links to).
- **How they clash while obeying the ADs:**
  - **The error:** AD-8 says "a business-rule error carrying the count and the affected date range". The code is not bound (`REPORTING_PERIOD_EXCLUDES_ACTIVITIES` appears only as a naming example in Conventions), and nothing says where the count and range sit in the envelope (`details` as a list or as an object).
  - **The range:** "the affected date range" is singular. Shrinking both ends strands activities on both sides, so a single min-to-max range also covers activities that are still inside. The linked list then shows 40 rows for a count of 6.
  - **The link:** AD-14 fixes only `?period=` and `?add=1`. M1 builds the link with `?from=&to=`, M2's page parses `?dateFrom=&dateTo=`, and the link opens an unfiltered list.
  - **The race:** the guard's count and a concurrent activity save in the old range can both commit under READ COMMITTED, leaving an activity stranded. That breaks M1's invariant ("no edit may break that"). Nothing in the database enforces date containment, unlike overlap, and the Deferred item "Concurrent-edit detection: last save wins" lets the race through.
- **Spine location:** AD-8 Rule, AD-3 (the guard's placement), AD-14 (address parameters), Deferred (concurrent edits).
- **Fix:**
  - **Tighten AD-8:**
    > A refused period edit returns 422 `REPORTING_PERIOD_EXCLUDES_ACTIVITIES` with one `details` entry per side: `{field: "startDate"|"endDate", issue: "excludes_activities", count, from, to}`, where `from` and `to` bound only the stranded activities on that side. The period edit and every activity save that links to a period both take `SELECT … FOR UPDATE` on the period rows involved before checking.
  - **Tighten AD-14:**
    > The Activities page address uses exactly `period`, `type`, `from`, `to` (YYYY-MM-DD), `sort`, `page`, `pageSize` and `add`. Other modules link to it only with these names.

### F9: AD-10 leaves out the paging envelope, the full list of unpaged collections, sort keys, tie-breakers and parameter names

- **Units:** M1 (reporting-periods list) vs M4 (period selector and previous period), M2 (period filter) and M5 (period selector). M2 (activity list) vs M5 (line items). M0 (the shared DataTable pager). M3 (factor history).
- **How they clash while obeying the ADs:**
  - **Unpaged collections:** only `GET /organizations` is exempt. The M1 builder pages `GET …/reporting-periods` at 25, as AD-10 says. M4's selector, its "period containing today" default and R8's "period that ended just before" read only page 1, and are silently wrong past 25 periods. The same applies to `GET /emission-factors` with version history (M3 R8, "history newest first").
  - **The envelope:** `{items, page, pageSize, total}` appears only in the detail doc. M5 returns `{lines, totalCount}`, while M0's pager expects one shape.
  - **Sort keys:** the keys decided in the memlog (`activityDate`, `activityType`, `quantity`, `emissionsKgCo2e`) never reached the spine.
  - **Tie-breakers:** none are defined, so sorting by date across many same-date rows makes pages unstable, with rows duplicated or missing between pages (M2's list, M5's line items).
  - **Parameter names:** the API filter is `periodId` in the detail doc and `period` in the UI address (AD-14), so M3's builder can pick either for the summary and monthly endpoints that M4 calls.
  - **`pageSize` over 100:** one builder clamps, another returns 422.
- **Spine location:** AD-10 Rule.
- **Fix (replace AD-10's paging and sorting sentences):**
  > Paged lists accept `page` (≥ 1) and `pageSize` (1–100, default 25; out of range gives 422 `VALIDATION_ERROR`), and return `{items, page, pageSize, total}` through M0's `Page[T]` schema and `pagination` dependency. The unpaged collections, which return `{items}`, are exactly: organizations (sorted by name), an organization's reporting periods (newest start first), emission factors with history, and one activity's calculation history. Sort keys are camelCase response field names (activities: `activityDate`, `activityType`, `quantity`, `emissionsKgCo2e`; report lines: `activityDate`). Every sort appends `id` ascending as the final tie-breaker. Filter parameters are named `periodId`, `activityType`, `from` and `to` on every endpoint that filters.

### F10: Inside the error envelope, nothing shared is defined

- **Units:** M3 (raises "no active factor … naming the type" from `EmissionCalculationService`, M3 R1) vs M2 (must show it at its field, M2 R2) vs M0 (frontend `ApiError` and the shared form-field mapping).
- **How they clash while obeying the ADs:**
  - **Undefined parts:** AD-10 lists the envelope's members only. Still open: whether the envelope is wrapped in `{"error": …}`; whether `details` is a list of `{field, issue}` or an object; field names in snake_case (the services are Python) or camelCase; and status codes. For example, an overlap could be 409 or 422, and M3 R7's "conflict error" has no status.
  - **The clash:** M3 raises `BusinessRuleError("NO_ACTIVE_FACTOR", details={"activity_type": "fuel"})`. M2's form looks for `details[].field == "activityType"` and finds nothing, so the error falls back to a page banner. That breaks M2 SM-3 ("refused … with a field-level error").
- **Spine location:** AD-10 Rule, last sentence; the Conventions row "Errors and logging".
- **Fix (replace AD-10's last sentence):**
  > Every error is `{"error": {"code", "message", "details": [{"field": <camelCase request field or null>, "issue": <snake_case>, …context}], "requestId"}}`. Status codes: 401 authentication; 404 unknown resource or one belonging to another organization; 409 uniqueness, overlap and simultaneous factor adds; 422 validation and business rules; 500 anything else. Codes are members of one enum in `app/core/error_codes.py`; M0 creates it, and each module adds its own codes there. Domain exceptions take the camelCase request field name.

### F11: Reference lists have no home beyond activity types, and enum wire values are not fixed

- **Units:** M0 (reference-data endpoint and country list) vs M1 (industry and country validation). M3 (activity types and scope labels) vs M2, M4 and M5 (display, chart series, CSV and file names).
- **How they clash while obeying the ADs:**
  - **Industries and countries:** AD-15 places only the activity-type list. M1 validates `country_code` with `^[A-Z]{2}$` while M0 serves a hand-made list, so the API accepts "XX", or it refuses a code the dropdown offers. M1 hardcodes its 12 industries as a `Literal[...]` in its schema while the reference data serves another copy.
  - **Activity type values:** no AD fixes them. Candidates are `business_travel` (the detail doc's CHECK), `businessTravel` (camelCase misapplied to values) and `business-travel` (kebab-case from URLs). M4 keys colours and series by type; M5 writes the type into file names and CSV rows.
  - **Units and scope labels:** units can be written `kWh` or `kwh`, `L` or `litre`. The scope label can be `"Scope 1"`, `1` or `"scope_1"`.
- **Spine location:** AD-15 Rule, last sentence; the Conventions Naming row.
- **Fix (replace AD-15's last sentence):**
  > Reference lists are constants with one owner each. Schemas, services, the seed and the reference-data service import them, and `GET /reference-data` serves them unchanged:
  > - activity types `electricity | fuel | business_travel`, with units `kWh | L | km` and scope labels `Scope 2 | Scope 1 | Scope 3`, in `app/calculation/activity_types.py` (M0 creates it; M3 adds the scope labels)
  > - industries in `app/core/reference/industries.py` (M1)
  > - countries, ISO 3166-1 alpha-2 with English names, in `app/core/reference/countries.py` (M0)
  >
  > These exact strings are the values in the database, JSON bodies, query parameters, URLs and CSVs; only display labels differ.

### F12: The disclaimer string has no owner, and the docs already disagree on it

- **Units:** M3 (the source label) vs M5 (the summary CSV's `source` column, "the standard demonstration label", M5 PRD) vs M2 and M4 (the on-screen disclaimer).
- **How they clash while obeying the ADs:** AD-6 says the server appends "the demonstration disclaimer" or uses "the standard label", but fixes neither string. The docs already carry two:
  - R31: "Demonstration factors, not official values"
  - detail doc §8.2 default: "Demonstration value, not an official emission factor"

  M3 builds labels from the second. M5 writes the first into the summary CSV. M2 and M4 hardcode their own UI text. M3's SM-2 test ("100% of source labels contain the demonstration disclaimer") checks one string while the CSV carries another.
- **Spine location:** AD-6 Rule; R31 has no AD across M2, M4 and M5.
- **Fix (append to AD-6):**
  > `DISCLAIMER` and `STANDARD_SOURCE_LABEL` are constants in `app/calculation/disclaimer.py` (M3). A source label is `f"{source_text} — {DISCLAIMER}"`, or `STANDARD_SOURCE_LABEL` when the text is blank. `GET /reference-data` returns `disclaimer`. The frontend shows it only through M3's `components/emissions/DisclaimerNote`. M5's summary-CSV `source` column uses `STANDARD_SOURCE_LABEL`.

### F13: The numeric ranges of M2 and M3 multiply past both the storage column and the JSON float

- **Units:** M2 (quantity at most 1,000,000,000, 3 dp; M2 R2) and M3 (factor at most 999,999.999999, 6 dp; M3 R7, plus the emissions column).
- **How they clash while obeying the ADs:**
  - **Storage:** quantity 1e9 and factor 999,999.999999 each pass validation, and their product is about 1.0e15 kg, which needs 16 integer digits. The emissions column the M3 builder takes from the detail doc, `NUMERIC(20,6)`, holds 14, so the insert overflows and a save that passed every rule returns `500 INTERNAL_ERROR`.
  - **JSON:** even below that limit, a JSON number (AD-5) holds about 15–16 significant digits. Per-row figures above about 1e9 kg at 6 dp, and totals above about 9e13 kg at 2 dp, are therefore inexact on screen while the CSV is exact. "Fixed precision" cannot be honoured.
  - **Column types:** the spine fixes none.
- **Spine location:** AD-5 Rule; Deferred, "Decimals as strings in the API".
- **Fix (append to AD-5):**
  > Column types: `quantity` and `quantity_used` NUMERIC(13,3); factor `value` and `factor_value_used` NUMERIC(12,6); `emissions_kg_co2e` NUMERIC(22,6); aggregates are unbounded NUMERIC.

  Then choose one of two options and state it in AD-5:
  - **(a)** Move "decimals as strings" out of Deferred and send every decimal as a string. At the permitted ranges, this is the only way the API's figures stay exact.
  - **(b)** Keep JSON numbers and record their limit as a known limitation: exact at 2 dp only below about 9e13 kg, and at 6 dp only below about 9e9 kg.

  Capping factors at a realistic maximum (for example 99.999999 kg CO2e per unit) removes the overflow but not the float limit.

### F14: The CSV download has no authenticated path, and "never written partially" is not achievable as written

- **Units:** M0 (`apiClient`, built for JSON, attaches the bearer token) and M5 (exports).
- **How they clash while obeying the ADs:**
  - **Authentication:** AD-13 puts a token on the export route, and that token is a bearer header, not a cookie, so a plain `<a href download>` gets a 401.
  - **The client:** AD-1 forbids pages calling `fetch`, so M5 must go through `apiClient`, which M0 built to parse JSON and map envelopes. M5 then either edits M0's `apiClient` (a cross-owner edit) or calls `fetch` in its own service with its own 401 handling, duplicating M0's session-expiry redirect (M0 R5). A token in the query string would leak into logs.
  - **Partial files:** once a streamed 200 has started, a mid-stream database error cannot become an envelope. An anchor download would save a truncated file, which contradicts AD-11's "never written partially".
- **Spine location:** AD-11 Rule, AD-1 (frontend data flow), AD-13.
- **Fix (append to AD-11):**
  > Exports download only through M0's `apiClient.download(path, params): Promise<{blob, filename}>`. It sends the bearer token, reads the whole body before handing it to the browser, and rejects on a non-2xx envelope or a truncated body. The server aborts the connection on any mid-stream error, so a partial file is never saved. The file name comes from `Content-Disposition`. Tokens never go in query strings.

### F15: The selected period has no owner for its default, its "All periods" value or how it is carried between pages

- **Units:** M0 (resets the period on switch, M0 R2; sidebar links), M2 (Activities with "All periods", M2 R4), M4 (the default rule, M4 R1) and M5 ("defaulting to the period selected on the dashboard", M5 R1).
- **How they clash while obeying the ADs:**
  - **Default:** AD-14 puts `?period=` in the address, but nobody owns what happens when it is absent. If M0's sidebar links are bare `/organizations/:orgId/activities` and drop the query, every page resolves its own default. M4 picks the period containing today, M2 treats absence as "All periods", and M5 has its own rule. "Most recent" can mean latest start or latest end.
  - **"All periods":** M2 encodes it as `?period=all`. M4 parses that as a UUID and shows its error state.
  - **Timing:** M0's reset-to-default (M0 R2) is built before M4 defines the default.
- **Spine location:** AD-14 Rule, first sentence.
- **Fix (append to AD-14's first sentence):**
  > M1 owns `useSelectedPeriod(orgId, {allowAll})` in `hooks/`. It reads `?period=`. When the value is absent or is not a period of this organization, it resolves the default: the period whose dates contain today's date (F19), else the one with the latest `end_date`. It writes that default to the address with history replace. `period=all` is valid only where `allowAll` is set (Activities); Dashboard and Reports treat it as absent. M0's sidebar links carry the current `period` parameter, and the organization switcher drops it.

### F16: Deleting an activity conflicts with calculation history that is "kept, never edited"

- **Units:** M3 (owns `emission_calculations` and its FK; AD-7 "earlier ones are kept, never edited") and M2 (delete, R22).
- **How they clash while obeying the ADs:** the M3 builder protects history with `ON DELETE RESTRICT`, which is consistent with "kept". M2's delete then fails with `IntegrityError` and returns 500. The alternative, where M2 deletes the calculation rows itself, writes M3's table and breaks AD-2. AD-3's single M2→M3 call is "on every save", so it does not cover deletes.
- **Spine location:** AD-7 Rule, AD-3 Rule.
- **Fix (append to AD-7):**
  > Deleting an activity removes its calculations through `ON DELETE CASCADE` on `emission_calculations.activity_record_id` in M3's migration. No service deletes calculations.

---

## Low

### F17: AD-3's wording forbids calls inside a module, and its read-anything clause cannot be enforced

- **Units:** M1 (intra-module), and M5 vs M2.
- **The problem:**
  - **Intra-module calls:** "Only these service-to-service calls are allowed" literally forbids `OrganizationService → ReportingPeriodService`, which M1 R1 needs to create an organization and its first period in one form. The M1 builder then duplicates the period rules, with different error codes, or breaks the AD.
  - **Read clause:** "Any module may read another module's table through its model" lets M5 write its own line-items query instead of calling `ActivityService`. M5's query can then differ from M2's list in filtering, current-calculation joins and sort, which puts M5 SM-1 at risk. Nothing checks that reads stay read-only.
- **Fix (replace AD-3's first and last sentences):**
  > These rules govern calls between modules; services within one module may call each other. Reading another module's table through its model is allowed only in: `ReportingPeriodService` (`activity_records`, for the guard), and `EmissionsQueryService` and `ReportService` (`reporting_periods` and `organizations`, for period dates and names). Everything else goes through the owning service. An import-linter contract in CI enforces AD-1 and AD-3.

### F18: Schema prerequisites span owners' migrations, and AD-2 forbids the fix

- **Units:** M1 vs M3 (the composite FK), and M3 vs M2 (indexes for the list).
- **The problem:**
  - **Composite FK:** AD-8's composite FK needs `UNIQUE (id, organization_id)` on `reporting_periods`. M1, built first, has no reason to create it, and M3 may not alter M1's table (AD-2).
  - **Indexes:** M2's list sorts by emissions and filters by type and date, so it needs indexes on M3's tables. AD-2 forbids M2 from adding them.
  - **`btree_gist`:** the extension has no named owner.
- **Fix:**
  - **Append to AD-8:**
    > M0's first migration enables `btree_gist`. M1's migration creates `UNIQUE (id, organization_id)` on `reporting_periods` as the target of the composite FK.
  - **Append to AD-2:**
    > M3's migrations include the indexes `activity_records (organization_id, activity_date, id)` and `(reporting_period_id, activity_type)`, and `emission_calculations (activity_record_id) WHERE is_current`. A non-owner that needs another index asks for it in the owner's next migration, and never alters the table itself.

### F19: "Today" is undefined

- **Units:** M2 server (the M2 PRD says "the server's calendar date") vs the M2 form and M4's default period, which use the client's local date.
- **The problem:** for users ahead of UTC, around midnight, the form accepts the local date and the server refuses it as a future date. M4's "period containing today" can differ from the server's view.
- **Fix (add a row to Conventions):**
  > "Today" is the server's date in `APP_TIMEZONE` (default UTC). `GET /reference-data` returns it as `today`, and the frontend uses that value for validation and defaults.

### F20: The operations dimension is partly silent (good-spine checklist)

- **Checklist item failed:** "no dimension the altitude owns (deployment, environments, operations) is left silent".
- **What is missing:**
  - **Startup failure:** the spine does not say what happens when migrate or seed fails on start (exit or keep serving; Compose restart policy).
  - **Migrations:** there is no Alembic chain policy, although three modules add migrations to one history.
  - **Reset:** there is no reset procedure. Because the seed never overwrites, `down -v` is the only way back to the golden data.
  - **Test database:** test database provisioning appears only in the detail doc.
  - **Performance dataset:** three modules need a 50,000-row dataset (M3 under AD-9, M4 SM-3, and M5 R4's 10-second export), and none owns it. Each will build its own generator with a different distribution.
  - **CORS origin:** the Structural Seed shows the browser at `127.0.0.1:5173`, while the detail doc's example origin is `http://localhost:5173`. Opening the address in the diagram is refused by CORS.
- **Fix (add AD-18, "Local operations"):**
  > - The backend starts with `alembic upgrade head && python -m app.seed && uvicorn`. Any failure exits non-zero, and the service has `restart: "no"`.
  > - Alembic has one linear history. Revision files are prefixed by module (`m0_`, `m1_`, `m3_`) and each one's `down_revision` is the current head.
  > - The reset is `docker compose down -v`, documented in the README.
  > - Tests use `${POSTGRES_DB}_test`, created and migrated by M0's session fixture.
  > - M3 owns `backend/tests/perf/make_dataset.py`: 50,000 activities in one organization and one 12-month period, all types, with current calculations. M4's and M5's performance checks reuse it.
  > - `CORS_ORIGINS` in `.env.example` lists both `http://localhost:5173` and `http://127.0.0.1:5173`.

### F21: Deferring design tokens lets each module pick its own colour per activity type

- **Units:** M2 (type badges), M4 (chart series) and M5.
- **Checklist item failed:** "nothing under Deferred lets two units diverge". Token values can wait for CP4, but the token names are a contract between modules now.
- **Fix (append to AD-14):**
  > Token names are fixed now; CP4 sets their values. The activity-type colours are `--color-activity-electricity`, `--color-activity-fuel` and `--color-activity-business-travel`. No component uses a raw colour.

### F22: It is unclear which route honours `?add=1`

- **Units:** M4 and M5 vs M2.
- **The problem:** "the add-activity flag (`?add=1`) [is] in the query string, shared by dashboard, activities and reports" can be read as each page honouring `?add=1` by mounting M2's dialog itself.
- **Fix (tighten AD-14):**
  > `?add=1` is honoured only by `/organizations/:orgId/activities`. Dashboard and Reports link to `/organizations/:orgId/activities?add=1&period=<id>`.

### F23: The shape of the calculation and factor in responses is not shared

- **Units:** M2 (activity rows and history) and M5 (line items), both showing "the factor used" (R25). M3's SM-2 test must check "100% of calculations returned by the API carry their factor version".
- **The problem:** M2 nests `emissions.factor`, while M5 flattens it into `factorValue` and `factorVersion`. A single API-wide test of M3's SM-2 has no shape to assert.
- **Fix (append to AD-7):**
  > M3's `schemas/emissions.py` defines `CalculationRead {emissionsKgCo2e, quantityUsed, calculatedAt, factor: FactorRef {id, value, unit, version, sourceLabel, createdAt}}`. Every response that contains a calculation embeds exactly this type.

---

## Good-spine checklist

| Check | Result | Findings |
| --- | --- | --- |
| Every AD's Rule is enforceable and prevents its stated divergence | **Fails.** AD-2 contradicts itself. AD-1 and AD-4 disagree on commits. AD-9 states an outcome with no mechanism. AD-14's "queries they affect" cannot be met across build order. AD-15 has no existence key. AD-5's "fixed precision" and "rounding only in the engine" are contradicted by shares and by the Pydantic default. AD-11's "never written partially" has no client mechanism. AD-3's read clause cannot be checked. | F1, F2, F3, F4, F6, F7, F14, F17 |
| Nothing under Deferred lets two units diverge | **Fails on three items.** "Concurrent-edit detection" allows the guard race. "Visual design … CP4" leaves token names open. "Decimals as strings" leaves the serializer and the range limits open. The other Deferred items are safe. | F8, F21, F6/F13 |
| R1–R45 are all covered | **Nominally yes:** every requirement falls in a Capability-map range. **Weak** where several modules share a requirement and no AD covers the seam: R4 (seed), R6 (industries and countries), R13/R37 (default period), R14 (headline seam), R16 (guard seam), R22 (delete cascade), R25 (factor shape), R31 (disclaimer). | F4, F11, F15, F5, F8, F16, F23, F12 |
| No dimension the altitude owns is silent | **Partly.** Deployment and environments are stated (local only, others deferred). Security is covered by AD-13 and AD-16. Operations (startup failure, migration chain, reset, perf dataset, CORS origin) is silent. Data-write ownership (F1), API internals (F9, F10) and client state (F7, F15) are underspecified. | F20, F1, F9, F10, F7, F15 |

## Builder's-eye note on the detail document

The six child initiatives list only the spine, the module PRD and the addendum as companions. The detail document is linked from the spine as "reference" but is not a companion, and several things builders need live only there: the page envelope, the error-envelope wrapper and status table, the emissions endpoints and their `periodId` parameter, column precisions, `ON DELETE CASCADE`, and the test-database setup. It also still carries stale text that its "known differences" banner does not list:
- §11.2 allowed calls: no M1→M3 edge
- §6.2 "a write service method commits"
- §8.2 `NUMERIC(20,6)`
- §8.2 the case-sensitive period-name unique constraint, superseded by AD-8

Either promote what the spine needs into ADs, as the fixes above do, or name the detail document as a binding companion section by section. Leaving it as non-binding reference is how two builders end up reading different contracts.
