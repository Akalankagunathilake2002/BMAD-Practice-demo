---
tracker_id: ""
type: initiative
title: "CarbonTrack: organizations record activity and see and report its carbon footprint"
parent: none
sources:
  - docs/initiative-carbontrack/prd-carbontrack/prd-carbontrack.md
  - docs/initiative-carbontrack/project-brief/project-brief.md
companions:
  - docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md
  - docs/initiative-carbontrack/assumptions-and-open-questions.md
after: []
risk: medium
---

# CarbonTrack: organizations record activity and see and report its carbon footprint

## Description

CarbonTrack lets an organization record its electricity, fuel and business-travel activity, converts it into kg CO2e with configurable demonstration factors, and shows the results on a dashboard and in period reports exported to CSV. It is a demo-grade product that also showcases a governed, AI-assisted SDLC (master PRD §1). This is a parent initiative: its six child initiatives, one per module, are listed in `tickets/tickets.toml` in build order.

## Outcome

A sustainability manager goes from a new organization to an explainable, exportable footprint in minutes, and a demo evaluator can trace every requirement to a story and a test (goals G1 and G5).

## Requirements

- R1 (FR-PLT-01, Must): App shell with a persistent sidebar (Dashboard, Activities, Reports, Organization, Emission Factors) and a top bar with an organization switcher. The sidebar collapses to a drawer on narrow screens.
- R2 (FR-PLT-02, Must): The backend serves interactive API documentation (Swagger UI) and the OpenAPI schema.
- R3 (FR-PLT-03, Must): Health endpoint reporting API and database status.
- R4 (FR-PLT-04, Must): Idempotent seed command that loads emission factors, demo user accounts, and fictional demo organizations with at least 12 months of activity.
- R5 (FR-PLT-05, Must): The whole stack starts locally with one docker compose up and is configured through environment variables.
- R6 (FR-PLT-06, Should): Reference-data endpoint (activity types with units, industries, countries) so the UI has a single source of truth.
- R7 (FR-AUTH-01, Must): Sign-in page (email and password) that checks credentials against seeded demo users. The page lists the demo accounts and states that sign-in is for demonstration only.
- R8 (FR-AUTH-02, Must): Visitors who are not signed in are redirected to the sign-in page, then returned to the page they asked for after signing in.
- R9 (FR-AUTH-03, Must): The top bar shows the signed-in user's name and role, with a Sign out action. The session survives page reloads and expires after 8 hours.
- R10 (FR-AUTH-04, Must): Seeded accounts represent the personas (Sustainability Manager, Data Contributor, Executive Viewer, Administrator). Roles are labels only and do not restrict actions.
- R11 (FR-AUTH-05, Should): Every API endpoint except health, sign-in and the API documentation requires a valid session token. Swagger UI can be authorized with that token.
- R12 (FR-ORG-01, Must): Create an organization with name (required, 2–200 characters, unique ignoring case), industry (required, from a predefined list), country (required, ISO 3166-1 alpha-2) and an initial reporting period.
- R13 (FR-ORG-02, Must): List organizations and switch the active organization. The choice persists across page reloads.
- R14 (FR-ORG-03, M (headline figures S)): View organization details: profile, reporting periods and headline figures (activity count, total emissions).
- R15 (FR-ORG-04, Must): Update name, industry and country, with the same validation as creation.
- R16 (FR-ORG-05, Must): Add and edit reporting periods. Period names are unique within an organization, the end date is on or after the start date, and an organization's periods never overlap.
- R17 (FR-ACT-01, Must): Record an activity with type (electricity, fuel, business travel), quantity, unit, reporting date and organization. The unit is determined by the type (kWh, L, km). An optional description of up to 500 characters can be added.
- R18 (FR-ACT-02, Must): Validation: quantity greater than 0, at most 1,000,000,000, with at most 3 decimal places; unit matches type; date not in the future; date falls inside one of the organization's reporting periods. Errors are shown next to the relevant field.
- R19 (FR-ACT-03, Must): Each activity is linked automatically to the reporting period that contains its date.
- R20 (FR-ACT-04, Must): List activities in a paginated, sortable table, filterable by activity type, reporting period and date range. Each row shows the calculated emissions and the factor used.
- R21 (FR-ACT-05, Must): Edit an activity. Its emissions are recalculated in the same operation.
- R22 (FR-ACT-06, Should): Delete an activity after confirmation. Its calculations are removed with it.
- R23 (FR-EMI-01, Must): Calculate emissions for each activity when it is created or updated: emissions (kg CO2e) = quantity × active emission factor for its type.
- R24 (FR-EMI-02, Must): Store every calculation result with a reference to the factor, the factor value and quantity used, and a timestamp. When an activity is edited, earlier results are kept as history rather than overwritten.
- R25 (FR-EMI-03, Must): Wherever a calculation is shown, display the factor used (value, unit, version, source label).
- R26 (FR-EMI-04, Must): Calculate total organizational emissions, overall and per reporting period.
- R27 (FR-EMI-05, Must): Group emissions by activity type, with each type's share of the total.
- R28 (FR-EMI-06, Must): Aggregate emissions by calendar month, and by type within each month, for trend views.
- R29 (FR-EMI-07, Must): Emission factors are configurable. Initial values load from a configuration file, new factor versions can be added through the API without code changes, and factor values are never edited in place.
- R30 (FR-EMI-08, Should): Emission Factors page listing active factors and their version history, with a form to add a new version.
- R31 (FR-EMI-09, Must): A visible disclaimer ("Demonstration factors, not official values") wherever factors or emissions appear, including CSV exports.
- R32 (FR-EMI-10, Should): On explicit request, recalculate all of an organization's activities using the currently active factors.
- R33 (FR-EMI-11, Could): Show the GHG scope label for each activity type (fuel: Scope 1, electricity: Scope 2, travel: Scope 3).
- R34 (FR-DSH-01, Must): Summary cards for the selected period: total emissions, plus electricity, fuel and travel emissions, each with its share of the total.
- R35 (FR-DSH-02, Must): Monthly emissions trend chart for the selected period, stacked by activity type.
- R36 (FR-DSH-03, Must): Emissions breakdown by activity type (donut chart).
- R37 (FR-DSH-04, Must): Reporting-period selector. It defaults to the period containing today, otherwise the most recent period.
- R38 (FR-DSH-05, Must): Loading skeletons, an empty state that prompts the user to add activity data, and an error state with a retry action.
- R39 (FR-DSH-06, Should): Interactive charts: tooltips with exact values and units, and legend items that toggle series.
- R40 (FR-DSH-07, Could): Comparison with the previous period (absolute and percentage change) on summary cards.
- R41 (FR-RPT-01, Must): Generate an emissions summary for a reporting period: total, by activity type and by month.
- R42 (FR-RPT-02, Must): Filter the report by reporting period (required) and activity type (optional).
- R43 (FR-RPT-03, Must): Show the report's activity line items: date, type, quantity, unit, factor used and emissions.
- R44 (FR-RPT-04, Must): Export the filtered line items as CSV: UTF-8, one header row, a descriptive file name, and per-row factor value, version and source label (the source label carries the demonstration disclaimer, see Q-10).
- R45 (FR-RPT-05, Should): Export the summary (by type and by month) as a second CSV.

## Constraints

- Calculation rules (master PRD §6): exact decimals, 6-decimal storage rounded half-up, versioned factors, demonstration values only.
- NFR-01 (Accuracy): Python Decimal and PostgreSQL NUMERIC are used for all emission arithmetic and storage. A golden-dataset test checks that seeded totals match expected values exactly.
- NFR-02 (Performance): Summary and trend endpoints respond within 500 ms (p95) with 50,000 activity rows on a developer laptop. List endpoints are paginated (default 25, maximum 100 per page).
- NFR-03 (Responsiveness): Usable from 360 px to 1920 px wide. Wide tables scroll inside their container, never the whole page.
- NFR-04 (Accessibility): WCAG 2.1 AA colour contrast, full keyboard operation and visible focus. Charts never rely on colour alone and have a text or table equivalent.
- NFR-05 (Error handling): All API errors use one JSON error envelope with a machine-readable code. No stack traces reach the client. The UI shows a readable message and a recovery action.
- NFR-06 (Security): No credentials in source code. Configuration comes from environment variables, with a committed .env.example. CORS is restricted to configured origins. All input is validated on the server; client-side validation is a convenience only. Demo user passwords are stored only as hashes, and session tokens are signed with a secret read from the environment. Mock login is documented as not production-grade.
- NFR-07 (Maintainability): Layered, modular code (architecture §6 and §10). TypeScript strict mode, Python type hints, and enforced linting and formatting (ruff, ESLint, Prettier).
- NFR-08 (Testability): Backend: unit tests for the calculation engine and services, and API tests for each endpoint's happy path and main error path, with at least 80% line coverage on services/ and calculation/. Frontend: tests for shared components, forms and page states.
- NFR-09 (Documentation): Root README with setup steps, OpenAPI documentation, and this docs/ folder kept current during course correction.
- NFR-10 (Portability): Runs with Docker Compose on macOS, Linux and Windows (WSL2), with no host dependencies other than Docker.
- NFR-11 (Traceability): Stories reference FR and NFR IDs. Test names or docstrings reference story IDs.

## Done when

- G1: A user can go from a new organization to a populated dashboard in under 5 minutes. Checked by: Walkthrough of journeys J1 and J2.
- G2: Every emission figure can be explained. Checked by: Each calculation shows quantity, factor value, factor version and result.
- G3: The app looks and behaves like an enterprise SaaS product. Checked by: CP4 design approval, plus loading, empty and error states on every data view.
- G4: The demo is reproducible. Checked by: docker compose up on a clean machine produces a seeded, working app.
- G5: The SDLC is traceable. Checked by: Every Must requirement maps to at least one story and at least one test.

## Boundaries

- Not this initiative: master PRD §2 non-goals and §10 out-of-scope list.
- Won't (this release): FR-AUTH-06: Self-registration, password reset, account lockout, role-based permissions, single sign-on.
- Won't (this release): FR-ORG-06: Delete or archive an organization.
- Won't (this release): FR-ACT-07: Bulk import from CSV or Excel.
- Won't (this release): FR-RPT-06: PDF export, and scheduled or emailed reports.
- Shared: R1 is delivered in parts by M0, M3; each child's Requirements line says which part.
- Shared: R4 is delivered in parts by M0, M3, M2; each child's Requirements line says which part.
- Shared: R6 is delivered in parts by M0, M1; each child's Requirements line says which part.
- Shared: R13 is delivered in parts by M0, M1; each child's Requirements line says which part.
- Shared: R14 is delivered in parts by M1, M3; each child's Requirements line says which part.
- Shared: R16 is delivered in parts by M1, M2; each child's Requirements line says which part.
- Shared: R17 is delivered in parts by M3, M2; each child's Requirements line says which part.
- Shared: R21 is delivered in parts by M3, M2; each child's Requirements line says which part.
- Shared: R23 is delivered in parts by M3, M2; each child's Requirements line says which part.
- Shared: R25 is delivered in parts by M3, M2, M5; each child's Requirements line says which part.
- Shared: R31 is delivered in parts by M3, M2, M4, M5; each child's Requirements line says which part.
- Shared: R33 is delivered in parts by M3, M2, M5; each child's Requirements line says which part.
- Shared: R38 is delivered in parts by M0, M4; each child's Requirements line says which part.
- Tracer path across children: sign in (M0) → create an organization and period (M1) → record an activity (M2), which M3 calculates → see it on the dashboard (M4) → report and export it (M5).

## References

- prd — docs/initiative-carbontrack/prd-carbontrack/prd-carbontrack.md, §5 Functional requirements
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11 Module boundaries and dependencies
- brief — docs/initiative-carbontrack/project-brief/project-brief.md
- decisions — docs/initiative-carbontrack/assumptions-and-open-questions.md, Decision log
- children — tickets/tickets.toml in this folder

## Notes

- Open question: the six module PRDs list API and schema gaps for the architect (each `addendum.md`, Architecture notes); update the architecture before slicing epics. Decision A also moves the activity table's owner from M2 to M3 (architecture §11.1).
- Open question: Q-09 (Figma or HTML prototype) blocks CP4.
- Decision: 2026-11-15, migrated from BMAD v6 to the v7 preview layout, one child initiative per module (docs/migration-v6-v7/migration-v6-v7.md).
- Decision: 2026-11-15, A: M3 builds the activity table and the calculation core first and tests them directly; M2 builds what users do with activities on top and calls the calculation in the same save. Architecture §11.1 (activity table owned by M2) must be updated.
- Decision: 2026-11-15, B: stories whose check needs a later module move to that module: demo-data seeding to M2, factor version 1 seeding to M3, headline figures to M3, the period-edit guard to M2. M0 keeps the seed command and demo users.
