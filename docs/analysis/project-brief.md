# CarbonTrack — Project Brief (Phase 1: Analysis)

| | |
|---|---|
| Version | 0.1 (draft) |
| Date | 2026-11-10 |
| Status | For review alongside CP1 |
| Input | Project requirements statement: *CarbonTrack – AI-Assisted Carbon Management Platform* |

## 1. Purpose

CarbonTrack has two purposes, and both shape the decisions in later documents:

1. **Product purpose.** A working, demo-grade carbon management SaaS. Organizations record activity data (electricity, fuel, business travel), the system converts it to CO2e using configurable factors, and users explore the results on a dashboard and in exportable reports.
2. **Process purpose.** A showcase of an AI-assisted SDLC using the BMAD method. Every phase produces a reviewable Markdown artifact, and a human approves each checkpoint before work continues.

Because of the second purpose, traceability (requirement → story → code → test) and visible decision records matter as much as the features themselves.

## 2. Problem statement

Organizations starting carbon reporting usually track activity data in spreadsheets. Units are inconsistent, factors are buried in formulas, nobody can tell which factor produced which number, and period summaries take manual effort. CarbonTrack demonstrates a simplified version of the loop that replaces this: structured capture, transparent calculation, visual insight and exportable reporting.

## 3. Users and stakeholders

| Persona | Who they are | What they need | Primary modules |
|---|---|---|---|
| **Sustainability Manager** (primary) | Owns the organization's carbon inventory | Set up the organization and its periods, check data quality, understand the footprint, produce reports | All |
| **Operations / Facilities Coordinator** | Holds the source data (utility bills, fuel cards, travel bookings) | Enter activity records quickly with the right units and fix mistakes | M2 Activity Data |
| **Executive / Finance Reviewer** | Consumes results and rarely enters data | Headline totals, trend and breakdown, plus a CSV to use in other tools | M4 Dashboard, M5 Reports |
| **Platform Administrator** | Maintains reference data (demo role) | Change demonstration emission factors without code changes while keeping history | M3 Emission factors |
| **Demo evaluator** (stakeholder, not an app user) | Assesses the AI SDLC demonstration | Clear artifacts, an approval trail, traceability and a professional-looking working app | `docs/`, whole repo |

In the MVP every persona uses the same unauthenticated interface. Roles describe users but are not enforced (A-01, Q-02).

## 4. Business requirements

| ID | Requirement |
|---|---|
| BR-01 | Maintain an organization profile (name, industry, country) and its reporting periods as the frame for all carbon data. |
| BR-02 | Capture activity data for electricity (kWh), fuel (litres) and business travel (km) against an organization and a date. |
| BR-03 | Convert activity data to kg CO2e transparently. Every result shows the factor that produced it and stays reproducible after factors change. |
| BR-04 | Show the footprint at a glance: total, per activity type, monthly trend and composition. |
| BR-05 | Produce reports filtered by reporting period, with line-item detail, exportable to CSV. |
| BR-06 | Keep emission factors configurable without code changes and clearly labelled as illustrative, not official. |
| BR-07 | Run locally with one command and realistic seed data, so the demo can be reproduced on any developer machine. |
| BR-08 | Demonstrate a governed AI SDLC: approved artifacts at six checkpoints and traceability from requirement to test. |

## 5. Domain concepts

| Term | Meaning in CarbonTrack |
|---|---|
| Activity data | A measured quantity of something that causes emissions, for example 12,400 kWh of electricity in March. |
| Emission factor | kg CO2e emitted per unit of activity. Demo values: electricity 0.5 per kWh, fuel 2.31 per litre, travel 0.12 per km. |
| CO2e | Carbon dioxide equivalent: greenhouse gases expressed as the amount of CO2 with the same warming effect. |
| Emission calculation | Quantity × factor, stored with the factor used and a timestamp. |
| Reporting period | A named date range (for example "FY2025", 2025-01-01 to 2025-12-31) that frames an organization's reporting. |
| GHG scope (informational) | Under the GHG Protocol, fuel combustion is typically Scope 1, purchased electricity Scope 2 and business travel Scope 3 (category 6). Shown as labels only if Q-13 is accepted. |

## 6. Constraints

- **Fixed technology.** React, TypeScript, Vite, Tailwind CSS and Recharts on the frontend; FastAPI and Pydantic on the backend; PostgreSQL with SQLAlchemy; Docker Compose for local development.
- **Demonstration only.** Factor values are illustrative. The product must not imply regulatory compliance or official accuracy.
- **Local-first.** The target environment is Docker Compose on a developer machine. Cloud deployment is out of scope.
- **Governed workflow.** Work stops at six human checkpoints, and no phase starts before the previous gate is approved.
- **Tooling availability.** The BMAD installer has not been run in this repository, and no Figma MCP connection is available in this session (Q-01, Q-09).

## 7. Ambiguities found in the source requirements

The requirements leave the following points open. Each one has a working assumption so planning can continue, and an open question so the reviewer can overrule it.

| Topic | Ambiguity | Working assumption | Ref |
|---|---|---|---|
| Reporting period | Listed both as an organization attribute and as its own table | An organization has many reporting periods, and the first one is created with the organization | A-03, Q-03 |
| Reporting date | Could mean the date of data entry or the date the activity happened | The date the activity happened (for a monthly bill, the end of the billing month) | A-05 |
| Period assignment | How an activity relates to a reporting period is not specified | Assigned automatically from its date. Dates outside every period are rejected | A-04, Q-04 |
| Units | A unit is stored, but only one unit per type is listed | One canonical unit per type, with no conversions in the MVP | A-06, Q-05 |
| Factor changes | The effect of a factor change on existing results is not specified | Existing results keep their original factor until an explicit recalculation | A-09, Q-06 |
| Users and access | No mention of login, roles or tenancy | No authentication in the MVP, and all organizations are visible | A-01, Q-02 |
| Edits and deletes | Only recording activities is required | Users can also edit and delete activities to correct entry errors | A-07 |

## 8. Risks

| Risk | Impact | Mitigation |
|---|---|---|
| Scope creep toward real carbon accounting (regional or yearly factors, market-based Scope 2, more emission sources) | Delays the demo | Explicit out-of-scope list in the PRD, with extension points noted in the architecture |
| Demo factors mistaken for real values | Misleading output | Disclaimer on every screen and export that shows factors (FR-EMI-09) |
| No authentication | Cannot be exposed publicly | Documented as a demo limitation. Ports are bound to localhost |
| Interface looks like a student project | Undermines the demo | Dedicated UX and design-system phase (CP4) before the build |
| BMAD and Figma tooling not set up | Process demo is less authentic | Artifacts follow the BMAD structure. Q-01 and Q-09 decide whether to install or connect the tools |

## 9. Out of scope (MVP)

Authentication and roles, multi-tenant data isolation, real or region-specific emission factors, unit conversion, Scope 3 categories beyond business travel, bulk import, PDF reports, targets and reduction planning, notifications, and cloud deployment.
