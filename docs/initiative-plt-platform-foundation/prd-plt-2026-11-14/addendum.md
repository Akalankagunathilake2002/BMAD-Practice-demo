# Addendum — Module PRD: Platform Foundation (M0)

Detail that supports [prd.md](prd-plt-2026-11-14.md) but belongs in the architecture, UX or stories. The decisions themselves are recorded in [.memlog.md](.memlog.md).

## Options considered

| Topic | Chosen | Not chosen |
|---|---|---|
| Demo data dates (PLT.FR-11) | **Fixed dates**, the same on every run | Dates relative to the day the seed runs |
| Demo reporting periods (PLT.FR-11) | **Calendar years**, named by year | April–March fiscal years; one organization of each kind |
| Where switching organization lands (PLT.FR-2) | **Same page, new organization** | Always the dashboard |
| First start (PLT.FR-12) | **Copy `.env.example` to `.env`, then `docker compose up`** | Compose falls back to placeholder values when `.env` is missing |

**Trade-off of fixed dates.** The demo is repeatable, and the seeded totals can be the golden dataset for NFR-01. But the data grows older: by 2027 the dashboard will open on the most recent period (2026) rather than one containing today (FR-DSH-04). Moving the window means changing the seed data and the golden values together.

## Demo data shape (for the seed story)

These are proposals. The story writer and developer settle the exact values.

- **Organizations:** 2 fictional organizations with different industries and countries, to show variety in the switcher and reports.
- **Periods:** "2025" (1 January to 31 December 2025) and "2026" (1 January to 31 December 2026) for each organization.
- **Activities, May 2025 to October 2026:**
  - Electricity: one record per month, dated the last day of the month (A-05), higher in winter and summer.
  - Fuel: one or two records per month, fairly steady.
  - Business travel: zero to four trips per month, irregular, so some months have no travel.
- **Volume:** enough rows for realistic charts. NFR-02's 50,000-row performance test uses a separate generated dataset, not the demo seed.
- **Demo accounts:** one per FR-AUTH-04 role, with emails like `sustainability.manager@example.com` and fictional display names.

## Engineering setup carried by the M0 epic

There are no user-facing requirements for these, but the M0 epic must deliver them:

- Continuous integration with GitHub Actions: lint and tests on every push (Q-12).
- Linting and formatting: ruff, ESLint and Prettier, enforced in CI (NFR-07).
- Database migrations with Alembic; the first migration enables `btree_gist` (architecture §8.3).
- A test database created and migrated by the pytest session fixture (architecture §13).
- A committed `.env.example` with every variable in architecture §12.1, using placeholder values only.
- Structured logging with the request ID (architecture §6.4).

## Architecture notes

- **Health check status codes (PLT.FR-9).** Architecture §9.2 does not say what the health endpoint returns when the database is down. The PRD assumes 503. Confirm when the architecture is updated.
- **Missing `.env` (PLT.FR-12).** Docker Compose already stops when a declared `env_file` is missing. The README should still show the copy step first.
