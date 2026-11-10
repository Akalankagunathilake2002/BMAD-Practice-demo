# CarbonTrack — Master Product Requirements Document

| | |
|---|---|
| Version | 0.2 |
| Date | 2026-11-10 |
| Status | **Approved at Checkpoint 1** (2026-11-10). v0.2 adds mock login, as decided in Q-02 |
| Source | [Project brief](../analysis/project-brief.md) |
| Related | [Master Architecture](architecture.md) · [Assumptions & open questions](assumptions-and-open-questions.md) |

## 1. Summary

CarbonTrack lets an organization record its electricity, fuel and business-travel activity, converts that activity into kg CO2e using configurable demonstration factors, and presents the results as a dashboard and as period-filtered reports that can be exported to CSV. It is a demo-grade product built to showcase an AI-assisted, BMAD-governed SDLC.

## 2. Goals

| ID | Goal | Measured by |
|---|---|---|
| G1 | A user can go from a new organization to a populated dashboard in under 5 minutes | Walkthrough of journeys J1 and J2 |
| G2 | Every emission figure can be explained | Each calculation shows quantity, factor value, factor version and result |
| G3 | The app looks and behaves like an enterprise SaaS product | CP4 design approval, plus loading, empty and error states on every data view |
| G4 | The demo is reproducible | `docker compose up` on a clean machine produces a seeded, working app |
| G5 | The SDLC is traceable | Every Must requirement maps to at least one story and at least one test |

**Non-goals:** accurate or audit-grade carbon accounting; compliance with any reporting framework (GHG Protocol, CSRD, SECR or others); production-grade authentication, security hardening, production deployment or multi-user collaboration.

## 3. Users

See [project brief §3](../analysis/project-brief.md#3-users-and-stakeholders). The primary user is the Sustainability Manager. Users sign in with seeded demo accounts, one per persona. Each account's role is shown in the UI but does not restrict what the user can do (A-01).

## 4. Modules

| ID | Module | Purpose | Backend | Frontend |
|---|---|---|---|---|
| M0 | Platform Foundation | Scaffolding, configuration, database, error handling, mock login, app shell, UI kit, seed data, Docker | ✓ | ✓ |
| M1 | Company Management | Organizations and their reporting periods | ✓ | ✓ |
| M2 | Activity Data Management | Record, list, edit and delete activity records | ✓ | ✓ |
| M3 | Emissions Calculation | Factors, calculation engine, stored results, aggregations | ✓ | ✓ (factors page) |
| M4 | Dashboard | Summary cards and charts | Uses M3 endpoints | ✓ |
| M5 | Reports | Period summaries, line items, CSV export | ✓ | ✓ |

M0 is not a module in the source requirements. It is added so cross-cutting work (Docker, layout, error handling, design system) has an owner and its own epic. Module boundaries and dependencies are defined in [architecture §11](architecture.md#11-module-boundaries-and-dependencies).

## 5. Functional requirements

Priorities use MoSCoW: **M**ust, **S**hould, **C**ould, **W**on't (this release).

### M0 — Platform Foundation

| ID | Requirement | Priority |
|---|---|---|
| FR-PLT-01 | App shell with a persistent sidebar (Dashboard, Activities, Reports, Organization, Emission Factors) and a top bar with an organization switcher. The sidebar collapses to a drawer on narrow screens. | M |
| FR-PLT-02 | The backend serves interactive API documentation (Swagger UI) and the OpenAPI schema. | M |
| FR-PLT-03 | Health endpoint reporting API and database status. | M |
| FR-PLT-04 | Idempotent seed command that loads emission factors, demo user accounts, and fictional demo organizations with at least 12 months of activity. | M |
| FR-PLT-05 | The whole stack starts locally with one `docker compose up` and is configured through environment variables. | M |
| FR-PLT-06 | Reference-data endpoint (activity types with units, industries, countries) so the UI has a single source of truth. | S |

### M0 — Mock authentication (added in v0.2, Q-02)

Demo-grade sign-in that makes the product feel real. It is not production security.

| ID | Requirement | Priority |
|---|---|---|
| FR-AUTH-01 | Sign-in page (email and password) that checks credentials against seeded demo users. The page lists the demo accounts and states that sign-in is for demonstration only. | M |
| FR-AUTH-02 | Visitors who are not signed in are redirected to the sign-in page, then returned to the page they asked for after signing in. | M |
| FR-AUTH-03 | The top bar shows the signed-in user's name and role, with a Sign out action. The session survives page reloads and expires after 8 hours. | M |
| FR-AUTH-04 | Seeded accounts represent the personas (Sustainability Manager, Data Contributor, Executive Viewer, Administrator). Roles are labels only and do not restrict actions. | M |
| FR-AUTH-05 | Every API endpoint except health, sign-in and the API documentation requires a valid session token. Swagger UI can be authorized with that token. | S |
| FR-AUTH-06 | Self-registration, password reset, account lockout, role-based permissions, single sign-on. | W |

### M1 — Company Management

| ID | Requirement | Priority |
|---|---|---|
| FR-ORG-01 | Create an organization with name (required, 2–200 characters, unique ignoring case), industry (required, from a predefined list), country (required, ISO 3166-1 alpha-2) and an initial reporting period. | M |
| FR-ORG-02 | List organizations and switch the active organization. The choice persists across page reloads. | M |
| FR-ORG-03 | View organization details: profile, reporting periods and headline figures (activity count, total emissions). | M (headline figures S) |
| FR-ORG-04 | Update name, industry and country, with the same validation as creation. | M |
| FR-ORG-05 | Add and edit reporting periods. Period names are unique within an organization, the end date is on or after the start date, and an organization's periods never overlap. | M |
| FR-ORG-06 | Delete or archive an organization. | W |

### M2 — Activity Data Management

| ID | Requirement | Priority |
|---|---|---|
| FR-ACT-01 | Record an activity with type (electricity, fuel, business travel), quantity, unit, reporting date and organization. The unit is determined by the type (kWh, L, km). An optional description of up to 500 characters can be added. | M |
| FR-ACT-02 | Validation: quantity greater than 0, at most 1,000,000,000, with at most 3 decimal places; unit matches type; date not in the future; date falls inside one of the organization's reporting periods. Errors are shown next to the relevant field. | M |
| FR-ACT-03 | Each activity is linked automatically to the reporting period that contains its date. | M |
| FR-ACT-04 | List activities in a paginated, sortable table, filterable by activity type, reporting period and date range. Each row shows the calculated emissions and the factor used. | M |
| FR-ACT-05 | Edit an activity. Its emissions are recalculated in the same operation. | M |
| FR-ACT-06 | Delete an activity after confirmation. Its calculations are removed with it. | S |
| FR-ACT-07 | Bulk import from CSV or Excel. | W |

### M3 — Emissions Calculation

| ID | Requirement | Priority |
|---|---|---|
| FR-EMI-01 | Calculate emissions for each activity when it is created or updated: `emissions (kg CO2e) = quantity × active emission factor for its type`. | M |
| FR-EMI-02 | Store every calculation result with a reference to the factor, the factor value and quantity used, and a timestamp. When an activity is edited, earlier results are kept as history rather than overwritten. | M |
| FR-EMI-03 | Wherever a calculation is shown, display the factor used (value, unit, version, source label). | M |
| FR-EMI-04 | Calculate total organizational emissions, overall and per reporting period. | M |
| FR-EMI-05 | Group emissions by activity type, with each type's share of the total. | M |
| FR-EMI-06 | Aggregate emissions by calendar month, and by type within each month, for trend views. | M |
| FR-EMI-07 | Emission factors are configurable. Initial values load from a configuration file, new factor versions can be added through the API without code changes, and factor values are never edited in place. | M |
| FR-EMI-08 | Emission Factors page listing active factors and their version history, with a form to add a new version. | S |
| FR-EMI-09 | A visible disclaimer ("Demonstration factors, not official values") wherever factors or emissions appear, including CSV exports. | M |
| FR-EMI-10 | On explicit request, recalculate all of an organization's activities using the currently active factors. | S |
| FR-EMI-11 | Show the GHG scope label for each activity type (fuel: Scope 1, electricity: Scope 2, travel: Scope 3). | C |

### M4 — Dashboard

| ID | Requirement | Priority |
|---|---|---|
| FR-DSH-01 | Summary cards for the selected period: total emissions, plus electricity, fuel and travel emissions, each with its share of the total. | M |
| FR-DSH-02 | Monthly emissions trend chart for the selected period, stacked by activity type. | M |
| FR-DSH-03 | Emissions breakdown by activity type (donut chart). | M |
| FR-DSH-04 | Reporting-period selector. It defaults to the period containing today, otherwise the most recent period. | M |
| FR-DSH-05 | Loading skeletons, an empty state that prompts the user to add activity data, and an error state with a retry action. | M |
| FR-DSH-06 | Interactive charts: tooltips with exact values and units, and legend items that toggle series. | S |
| FR-DSH-07 | Comparison with the previous period (absolute and percentage change) on summary cards. | C |

### M5 — Reports

| ID | Requirement | Priority |
|---|---|---|
| FR-RPT-01 | Generate an emissions summary for a reporting period: total, by activity type and by month. | M |
| FR-RPT-02 | Filter the report by reporting period (required) and activity type (optional). | M |
| FR-RPT-03 | Show the report's activity line items: date, type, quantity, unit, factor used and emissions. | M |
| FR-RPT-04 | Export the filtered line items as CSV: UTF-8, one header row, a descriptive file name, and per-row factor value, version and source label (the source label carries the demonstration disclaimer, see Q-10). | M |
| FR-RPT-05 | Export the summary (by type and by month) as a second CSV. | S |
| FR-RPT-06 | PDF export, and scheduled or emailed reports. | W |

## 6. Calculation rules

| Activity type | Unit | Demo factor (kg CO2e per unit) | Scope label (FR-EMI-11) |
|---|---|---|---|
| Electricity | kWh | 0.5 | Scope 2 |
| Fuel | L (litres) | 2.31 | Scope 1 |
| Business travel | km | 0.12 | Scope 3 |

- Formula: `emissions_kg_co2e = quantity × factor_value`.
- Calculation and storage use exact decimals throughout. Binary floating point is never used for emission arithmetic.
- Results are stored to 6 decimal places, rounded half-up. Tables display kg CO2e to 2 decimals; dashboard cards display tonnes CO2e to 2 decimals (Q-11).
- Changing a factor creates a new factor version. Existing results keep their original factor until they are recalculated (FR-EMI-10, Q-06).
- Worked example: 12,400 kWh × 0.5 = 6,200 kg CO2e = 6.20 t CO2e.

These values are illustrative and for demonstration only. They are not official or universally applicable emission factors.

## 7. Key user journeys

- **J1 — Onboard an organization.** Sign in with a demo account. Create an organization (name, industry, country, first reporting period). The user lands on its empty dashboard, where the empty state prompts "Add activity data".
- **J2 — Record monthly activity.** Activities → Add activity → choose a type (the unit fills in automatically) → enter quantity and date → save. The new row shows its emissions and the factor used, and the dashboard figures update.
- **J3 — Review and report.** Dashboard → choose a period → read the cards, trend and breakdown → Reports → filter by period and type → review line items → Export CSV.
- **J4 — Update a factor (admin).** Emission Factors → add a new electricity version. New activities use it. Existing results stay unchanged until "Recalculate" is run.

## 8. Non-functional requirements

| ID | Category | Requirement |
|---|---|---|
| NFR-01 | Accuracy | Python `Decimal` and PostgreSQL `NUMERIC` are used for all emission arithmetic and storage. A golden-dataset test checks that seeded totals match expected values exactly. |
| NFR-02 | Performance | Summary and trend endpoints respond within 500 ms (p95) with 50,000 activity rows on a developer laptop. List endpoints are paginated (default 25, maximum 100 per page). |
| NFR-03 | Responsiveness | Usable from 360 px to 1920 px wide. Wide tables scroll inside their container, never the whole page. |
| NFR-04 | Accessibility | WCAG 2.1 AA colour contrast, full keyboard operation and visible focus. Charts never rely on colour alone and have a text or table equivalent. |
| NFR-05 | Error handling | All API errors use one JSON error envelope with a machine-readable code. No stack traces reach the client. The UI shows a readable message and a recovery action. |
| NFR-06 | Security | No credentials in source code. Configuration comes from environment variables, with a committed `.env.example`. CORS is restricted to configured origins. All input is validated on the server; client-side validation is a convenience only. Demo user passwords are stored only as hashes, and session tokens are signed with a secret read from the environment. Mock login is documented as not production-grade. |
| NFR-07 | Maintainability | Layered, modular code (architecture §6 and §10). TypeScript `strict` mode, Python type hints, and enforced linting and formatting (ruff, ESLint, Prettier). |
| NFR-08 | Testability | Backend: unit tests for the calculation engine and services, and API tests for each endpoint's happy path and main error path, with at least 80% line coverage on `services/` and `calculation/`. Frontend: tests for shared components, forms and page states. |
| NFR-09 | Documentation | Root README with setup steps, OpenAPI documentation, and this `docs/` folder kept current during course correction. |
| NFR-10 | Portability | Runs with Docker Compose on macOS, Linux and Windows (WSL2), with no host dependencies other than Docker. |
| NFR-11 | Traceability | Stories reference FR and NFR IDs. Test names or docstrings reference story IDs. |

## 9. MVP acceptance criteria

1. All **Must** requirements are implemented and demonstrated through journeys J1–J3.
2. Each Must requirement is traced to at least one user story and at least one automated test.
3. `docker compose up` on a clean checkout starts the database, API and UI with seed data, and the README steps have been verified.
4. Backend and frontend test suites pass.
5. CP1 to CP6 are recorded as approved in [docs/README.md](../README.md).

## 10. Out of scope

Production authentication (registration, password reset, SSO) and role-based permissions; deleting organizations; multi-tenant isolation; region- or year-specific factors; unit conversion; fuel subtypes and travel modes; bulk import; PDF, scheduled or emailed reports; targets and reduction planning; notifications; cloud deployment.

## 11. Open items affecting this PRD

All CP1 questions were resolved at approval. See the decision log in [assumptions-and-open-questions.md](assumptions-and-open-questions.md). Q-16 (industry list) and Q-18 (Emission Factors page) are confirmed in the module PRDs at CP3.

## Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-11-10 | Initial draft for CP1 |
| 0.2 | 2026-11-10 | CP1 approved. Q-02 resolved as mock login: added FR-AUTH-01 to 06, demo users in seed data (FR-PLT-04), sign-in step in J1, hashing and signing rules in NFR-06, and updated non-goals and out-of-scope |
