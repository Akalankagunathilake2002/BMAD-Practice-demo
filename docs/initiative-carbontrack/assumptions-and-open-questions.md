# CarbonTrack — Assumptions and Open Questions

| | |
|---|---|
| Version | 0.2 |
| Date | 2026-11-10 |
| Status | Living document. Updated at every checkpoint |

Approving a checkpoint without comment accepts the **recommended** option for each question that blocks it. To choose differently, note the question ID and the option in the approval log in [docs/README.md](../README.md). The append-only BMAD decision trail is in [.memlog.md](.memlog.md).

## Assumptions

| ID | Assumption | Why | If wrong |
|---|---|---|---|
| A-01 | Mock login with seeded demo users, one per persona. Roles are shown but not enforced. (Updated in v0.2 per Q-02.) | Chosen at CP1 | Real authentication becomes its own module |
| A-02 | Several organizations can exist and the user switches between them. Queries are scoped by organization, with no stronger isolation. Every demo user can see every organization. | "Create organizations" implies more than one | Tenancy model needed |
| A-03 | An organization has one or more reporting periods. The first is created together with the organization. Periods of one organization never overlap. | Reconciles "organization has a reporting period" with the `reporting_periods` table | Simplify to a single period field |
| A-04 | An activity is assigned to the period that contains its date. Dates outside every period are rejected. | Keeps reports complete and consistent | Allow unassigned activities |
| A-05 | "Reporting date" means the date the activity happened. For a monthly bill, use the last day of the billing month. Trends are grouped by this date. | Required for meaningful monthly trends | Trend semantics change |
| A-06 | One canonical unit per type (kWh, L, km). The unit is stored for traceability and future conversion. | Requirements list one unit per type | Add conversion tables |
| A-07 | Activities can be edited and deleted to correct entry errors. Edits trigger recalculation and keep calculation history. | Realistic data entry needs corrections | Remove edit and delete stories |
| A-08 | One active factor per activity type, not per country or year, with the values given in the requirements. | Matches the requirements | Factor-selection logic grows |
| A-09 | Changing a factor does not alter stored results. An explicit recalculation does. | Reproducibility | Auto-recalculation |
| A-10 | Emissions are stored in kg CO2e. The dashboard shows tonnes, and tables show kg. | Readable headline numbers alongside precise detail | Display rules only |
| A-11 | English-only UI. Numbers use thousands separators through `Intl`. Activity dates are calendar dates with no time zone. Timestamps are UTC. | Demo scope | i18n work |
| A-12 | Local Docker Compose is the only deployment target. | Requirements specify local development | Add deployment architecture |
| A-13 | Seed data is entirely fictional: 2 organizations with 18 months of activity each, plus 4 demo users. | Shows trends and period comparison without real company data | Adjust volume |
| A-14 | Industry is chosen from a predefined list of about 12 values. Country is chosen from ISO 3166-1. | Clean grouping and validation | Free text (Q-16) |
| A-15 | Desktop-first design that remains usable on tablets and phones. | Enterprise SaaS usage pattern | UX phase rebalances |
| A-16 | BMAD Method v6.12.4 (latest stable) is installed with the BMM module for Claude Code. Its artifacts go to `docs/planning` (planning) and `docs/implementation` (stories, sprint status, reviews). The v0.1 PRD and architecture, written before the install, are adopted as the BMAD planning artifacts. | Q-01 decision. BMAD v7 is only available as a beta preview | Migrate to v7 when it is stable (`bmad migrate`) |
| A-17 | The sign-in page lists the demo account emails. The shared demo password lives in `.env` and the README, not in the UI or the frontend bundle. | Keeps the secret out of the client while staying easy to demo | Show the password on the page |

## Open questions

| ID | Question | Options | Recommendation | Blocks |
|---|---|---|---|---|
| Q-09 | Figma designs. No Figma MCP connection is available in this session. | (a) Connect the Figma MCP before the UX phase, and the designs are produced in Figma. (b) BMAD UX specification and design-system document, plus a clickable HTML prototype as the reviewable design. | (a) if you can connect Figma, otherwise (b) | CP4 |
| Q-16 | Industry field | (a) Predefined list. (b) Free text. | (a) | CP3 |
| Q-18 | Emission Factors UI (FR-EMI-08, Should) | (a) Page with history and an "add version" form. (b) API and Swagger only. | (a) | CP3 |

## Decision log

| ID | Decision | Date | Decided by |
|---|---|---|---|
| CP1 | Master PRD approved. Q-02 changed the scope, so the PRD moved to v0.2 | 2026-11-10 | Akalanka |
| CP2 | Master Architecture approved. Moved to v0.2 for mock login and the BMAD layout | 2026-11-10 | Akalanka |
| Q-01 | Install BMAD Method. v6.12.4 (latest stable) installed, because v7 is only in beta preview | 2026-11-10 | Akalanka |
| Q-02 | Mock login with seeded demo users (option b) | 2026-11-10 | Akalanka |
| Q-03 | Many reporting periods per organization (a) | 2026-11-10 | Accepted at CP1 |
| Q-04 | Reject activities dated outside every period (a) | 2026-11-10 | Accepted at CP1 |
| Q-05 | Canonical units only (a) | 2026-11-10 | Accepted at CP1 |
| Q-06 | Keep existing results until an explicit recalculation (a) | 2026-11-10 | Accepted at CP1 |
| Q-07 | One factor each for fuel and travel (a) | 2026-11-10 | Accepted at CP1 |
| Q-08 | Stack additions approved: Alembic, React Router, TanStack Query, Vitest + RTL + MSW, pytest + httpx, ruff, ESLint + Prettier, `btree_gist` | 2026-11-10 | Accepted at CP2 |
| Q-10 | Per-row `factor_source` column carries the CSV disclaimer (a) | 2026-11-10 | Accepted at CP1 |
| Q-11 | Tonnes on dashboard cards, kg in tables and CSV (a) | 2026-11-10 | Accepted at CP1 |
| Q-12 | GitHub Actions CI for lint and tests (a) | 2026-11-10 | Accepted at CP2 |
| Q-13 | Show GHG scope labels as a Could requirement (a) | 2026-11-10 | Accepted at CP1 |
| Q-14 | Seed 2 organizations × 18 months (a) | 2026-11-10 | Accepted at CP2 |
| Q-15 | No organization delete or archive in the MVP (a) | 2026-11-10 | Accepted at CP1 |
| Q-17 | One branch and pull request per checkpoint (b) | 2026-11-10 | Akalanka |
