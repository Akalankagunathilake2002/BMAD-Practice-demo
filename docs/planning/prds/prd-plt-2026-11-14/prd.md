---
title: "CarbonTrack — Module PRD: Platform Foundation (M0)"
status: final
created: 2026-11-14
updated: 2026-11-15
module: M0
slug: plt
parent: docs/planning/prd.md
---

# CarbonTrack — Module PRD: Platform Foundation (M0)

| | |
|---|---|
| Module | M0 Platform Foundation, slug `plt` |
| Status | Final, awaiting CP3 approval (merge of the CP3 pull request, Q-17) |
| Scope | 14 FRs: PLT.FR-1 to 6, 8, 9 and 11 to 14 MVP; PLT.FR-7 and 10 Should (§6) |
| Parent | [Master PRD v0.2](../../prd.md) (approved at CP1) |
| Constraints from | [Master Architecture v0.2](../../architecture.md) (approved at CP2) |
| Checkpoint | CP3 Module PRDs |
| Decision log | [.memlog.md](.memlog.md) · Supporting detail in [addendum.md](addendum.md) |

## 0. Document purpose

This PRD details what M0 must do so that UX (CP4) and epics and stories (CP5) can be written from it. It does not repeat the master PRD. Where a rule already exists there or in the architecture, this document cites its ID (for example FR-PLT-01, FR-AUTH-03, NFR-05, G4, Q-14, A-17) and adds only what is left open.

New functional requirements are numbered **PLT.FR-1** onward. Each carries a tag:

- **MVP**: refines a Must requirement or a master NFR, so it is part of the MVP (master §9).
- **Should**: refines a master requirement with that priority.

Rules inferred where the source documents are silent are marked `[ASSUMPTION]` and indexed in §9. All of them were accepted at the draft review on 2026-11-15. The tags stay so that story refinement can trace them.

All master non-functional requirements apply to M0. M0 is where most of them are first built: NFR-03 (responsive shell), NFR-05 (error handling), NFR-06 (security), NFR-07 (code quality), NFR-09 (README) and NFR-10 (portability).

## 1. Vision

M0 is everything the user meets before and around the carbon features: starting the app with one command, signing in with a demo account, and moving around a professional app shell. It also holds the shared pieces every other module depends on: the standard error format, the loading, empty and error states, the reference data, and the seed data that makes the demo look real from the first minute.

M0 serves two goals directly. G4: the demo starts the same way on any clean machine. G3: it looks and behaves like an enterprise SaaS product. M0 has no carbon logic of its own.

## 2. Users and journeys

Every persona in [project brief §3](../../../analysis/project-brief.md#3-users-and-stakeholders) uses M0, because every visit starts with sign-in and the app shell. Two matter most here:

- **Demo evaluator.** Clones the repository, follows the README and expects a working, populated app (G4, MVP acceptance criterion 3).
- **Sustainability Manager.** Signs in with a demo account and switches between the two demo organizations.

M0 realizes the start of master journey **J1** (sign in) and provides the navigation every other journey uses.

## 3. Glossary

Terms from the master PRD and the brief keep their meaning. This PRD adds:

- **App shell.** The frame around every signed-in page: sidebar, top bar and content area (FR-PLT-01).
- **Active organization.** The organization whose data the page shows. It is part of the page address (architecture §10.2).
- **Organization switcher.** The control in the top bar that changes the active organization.
- **Demo account.** One of the seeded user accounts, one per role in FR-AUTH-04.
- **Session.** The period during which a signed-in user can use the app without signing in again. It starts at sign-in and lasts 8 hours (FR-AUTH-03).
- **Session token.** What the browser holds during a session and sends with every API call (architecture §6.5).
- **Seed command.** The command that loads factors, demo accounts and demo data (FR-PLT-04).
- **Demo data.** The fictional organizations, reporting periods and activities created by the seed command (Q-14, A-13).
- **Reference data.** The lists the UI picks from: activity types with units and scope labels, industries and countries (FR-PLT-06).
- **Error envelope.** The single format every API error uses (NFR-05, architecture §6.3).
- **Request ID.** The identifier attached to every API response, which links an error the user sees to a log line (architecture §6.4).
- **Page state.** One of the four states every data view goes through: loading, error, empty or content (architecture §10.3).

## 4. Features

### 4.1 App shell and navigation

Refines FR-PLT-01 and FR-ORG-02 (M1 owns the organization list; M0 hosts the switcher).

#### PLT.FR-1 · MVP: App shell

Every signed-in page sits inside the app shell.

**Consequences (testable):**
- The sidebar shows the entries listed in FR-PLT-01 and highlights the one for the current page.
- The top bar shows the organization switcher and the signed-in user (PLT.FR-5).
- On narrow screens the sidebar becomes a drawer, opened from the top bar. The exact breakpoint is set in UX (CP4), within NFR-03.
- The sidebar, the drawer and the organization switcher work fully from the keyboard, with visible focus (NFR-04).
- The sign-in page does not use the app shell.
- `[ASSUMPTION]` If the Emission Factors page (EMI.FR-8, Should) is not built, its sidebar entry is hidden rather than leading nowhere. This resolves M3's open question 2.

#### PLT.FR-2 · MVP: Switch organization

The organization switcher shows the active organization and lists every organization (A-02).

**Consequences (testable):**
- Choosing another organization keeps the user on the same page for the new organization. For example, Activities for organization A becomes Activities for organization B.
- The selected reporting period resets to the new organization's default (FR-DSH-04), because periods belong to one organization.
- `[ASSUMPTION]` On pages that are not tied to an organization (Emission Factors, organization list), switching changes the active organization but keeps the page.
- `[ASSUMPTION]` When no organization exists, the switcher offers "Create organization", and the organization-scoped sidebar entries lead to the organization list.

#### PLT.FR-3 · MVP: Addresses, redirects and not-found

Every page has its own address, so views can be linked and bookmarked (architecture §10.2).

**Consequences (testable):**
- `/` opens the dashboard of the last-used organization, or the organization list if there is none.
- An unknown address, or an organization that does not exist, shows a not-found page with a link back to `/`.

### 4.2 Mock sign-in

Refines FR-AUTH-01 to FR-AUTH-05. Demonstration only: it is not production security (non-goals, §5).

#### PLT.FR-4 · MVP: Sign in and return

A visitor signs in with a demo account and lands on the page they asked for. Refines FR-AUTH-01 and FR-AUTH-02.

**Consequences (testable):**
- The sign-in page lists the demo account emails with their roles and says that sign-in is for demonstration only. It never shows the password (A-17).
- Email matching ignores letter case.
- `[ASSUMPTION]` A wrong email or password shows one message that does not say which of the two was wrong.
- A visitor who is not signed in and opens any app page is sent to sign-in, and after signing in returns to that page.
- `[ASSUMPTION]` Only paths inside the app are accepted as the return page. Anything else returns the user to `/`.
- `[ASSUMPTION]` Opening the sign-in page while signed in goes to `/`.

#### PLT.FR-5 · MVP: Session and sign-out

Refines FR-AUTH-03.

**Consequences (testable):**
- The top bar shows the user's name and role, and a Sign out action.
- Reloading the page keeps the user signed in.
- The session ends 8 hours after sign-in, whatever the user does. There is no "stay signed in" and no automatic extension.
- `[ASSUMPTION]` When the session ends mid-use, the next action sends the user to sign-in with the message "Your session has expired", and after signing in they return to the same page. Unsaved form input is lost.
- Sign out returns the user to the sign-in page.

#### PLT.FR-6 · MVP: Demo accounts

The seed command creates one demo account for each role in FR-AUTH-04.

**Consequences (testable):**
- All demo accounts share one password, set through the environment (architecture §12.1). It appears in the README and the environment file, never in the UI or the frontend code (A-17).
- Passwords are stored only as hashes (NFR-06).
- Roles are labels only. Every demo account can do everything (FR-AUTH-04, A-01).
- `[ASSUMPTION]` Demo account emails use the reserved `example.com` domain, so they can never reach a real mailbox.

#### PLT.FR-7 · Should: Token on every API call

Refines FR-AUTH-05.

**Consequences (testable):**
- Every API endpoint except health, sign-in and the API documentation refuses a request without a valid session token, with HTTP 401 and the error envelope.
- The error code says whether the token was missing, invalid or expired (architecture §6.3).
- The API documentation's Authorize action accepts a token, so endpoints can be tried from the browser.

### 4.3 Platform services

#### PLT.FR-8 · MVP: API documentation

Refines FR-PLT-02.

**Consequences (testable):**
- Interactive API documentation and the machine-readable API schema are served by the backend (architecture §9.1).
- Every endpoint has a summary, a description and at least one example.

#### PLT.FR-9 · MVP: Health check

Refines FR-PLT-03.

**Consequences (testable):**
- The health check works without a session token.
- `[ASSUMPTION]` It returns HTTP 200 when the API and the database are both reachable, and HTTP 503 when the database is not. The body states the status of each.

#### PLT.FR-10 · Should: Reference data

Refines FR-PLT-06. Every screen picks from the same lists.

**Consequences (testable):**
- It returns the activity types with their units and scope labels (EMI.FR-10), the industry list and the country list (ISO 3166-1, English names, A-11).
- The industry list is whatever the M1 module PRD settles for Q-16. M0 serves it; it does not define it.

### 4.4 Seed data and local start

Realizes G4.

#### PLT.FR-11 · MVP: Seed command

Refines FR-PLT-04, Q-14 and A-13.

**Consequences (testable):**
- Running the seed command twice leaves the data exactly as after the first run: no duplicates, and nothing changed.
- The seed command runs on every start (PLT.FR-12), so changes users make to demo data survive a restart.
- It creates version 1 of each emission factor only when that activity type has no factor yet (architecture §8.3).
- Demo accounts and demo data are created only when demo seeding is switched on in the environment.
- Demo data uses **fixed dates**: every run produces the same organizations, periods, activities and totals. `[ASSUMPTION]` The data covers the 18 full months from May 2025 to October 2026.
- Each demo organization has one reporting period per calendar year, named by its year (for example "2025"). The periods cover every demo activity date (A-04).
- All demo data passes the same validation as data entered in the app (FR-ORG-01, FR-ORG-05, FR-ACT-02), so it never shows something a user could not create.
- Because the dates and quantities are fixed, the NFR-01 golden test can check the seeded totals exactly.
- The cost of fixed dates: the demo data ages. Once the calendar moves past its last period, the dashboard opens on the most recent period rather than one containing today (FR-DSH-04). Moving the window means updating the seed data and the golden values together.
- `[ASSUMPTION]` If a demo organization with the same name already exists, its data is left untouched.
- `[ASSUMPTION]` The demo activity follows a believable monthly pattern (seasonal electricity, steady fuel, irregular travel) so the charts show real trends. The pattern is in the addendum.

#### PLT.FR-12 · MVP: One-command start

Refines FR-PLT-05, NFR-09 and NFR-10.

**Consequences (testable):**
- On a clean machine with only Docker installed, two steps give a working, seeded app: copy the example environment file to `.env`, then run `docker compose up`.
- The README lists these steps, the demo account emails, and where to find the password.
- On start, the database schema is brought up to date and the seed command runs before the API accepts requests.
- The app and the API are reachable only from the same machine (architecture §12.2).
- The API accepts browser requests only from the origins listed in the environment (NFR-06).
- `[ASSUMPTION]` If `.env` is missing, the start stops with a message that names the missing file and the copy step.

### 4.5 Shared behavior for every module

Master §4 assigns error handling and the shared UI kit to M0.

#### PLT.FR-13 · MVP: Errors and request IDs

Refines NFR-05.

**Consequences (testable):**
- Every API error uses the error envelope: a machine-readable code, a readable message, field-level details where they apply, and the request ID.
- Every API response, successful or not, carries a request ID.
- An unexpected server error returns a generic message, never a stack trace or SQL (NFR-06), and is logged with its request ID.
- The UI shows every error as a plain-language message with a recovery action, such as Retry.
- `[ASSUMPTION]` Error panels show the request ID as a small reference, so a problem seen in a demo can be traced in the logs.

#### PLT.FR-14 · MVP: Shared page states

Refines G3 and FR-DSH-05.

**Consequences (testable):**
- M0 provides one shared component for each page state: a loading skeleton, an error panel with Retry, and an empty state with one main action.
- Every data view in M1 to M5 uses these components rather than building its own.
- How they look is set in UX (CP4).

## 5. Non-goals

The master PRD's non-goals (§2) and out-of-scope list (§10) apply. In particular, M0 does **not** provide:

- self-registration, password reset, account lockout, permissions by role or single sign-on (FR-AUTH-06);
- sign-out on the server: a discarded session token stays valid until it expires (architecture §6.5);
- deployment anywhere other than a developer's machine (A-12);
- any business feature of M1 to M5. M0 hosts their pages and data but does not define them.

## 6. MVP scope

**MVP:** PLT.FR-1 to PLT.FR-6, PLT.FR-8, PLT.FR-9 and PLT.FR-11 to PLT.FR-14.
**Should:** PLT.FR-7 (token on every API call) and PLT.FR-10 (reference data).

Without PLT.FR-7, the UI still requires sign-in, but the API itself is open to anyone on the same machine.

## 7. What M0 provides to other modules

| Module | Receives from M0 | Note |
|---|---|---|
| All | The app shell, page addresses, the sign-in guard, the error envelope and the shared page states | Each module adds its pages inside the shell |
| M1 Company | The organization switcher, and seeded demo organizations and periods | M1 owns the organization rules; the seed follows them |
| M2 Activities | Seeded demo activities, and the activity types and units from reference data | Seeded activities pass M2's validation |
| M3 Emissions | Factor version 1 for each type, loaded by the seed command | M3 owns factor rules (EMI.FR-7) |
| M4, M5 | Demo data that produces real trends, and the shared page states | Fixed dates keep the demo totals the same on every run |

M0 also carries the engineering setup that has no user-facing requirement: continuous integration on every push (Q-12), linting and formatting (NFR-07), database migrations, and the test database. These are listed in the [addendum](addendum.md).

## 8. Success metrics

- **SM-1: Clean-machine start.** Following the README on a clean machine gives a working, seeded app with no other steps. Validates PLT.FR-11 and PLT.FR-12, and goal G4.
- **SM-2: Repeatable seed.** Running the seed command a second time changes no rows. Validates PLT.FR-11.
- **SM-3: Exact demo totals.** The seeded totals match the golden values exactly (NFR-01). Validates PLT.FR-11.
- **SM-C1 (counter-metric): An easier start must not weaken the safeguards.** Do not add committed secrets or fallback values to remove the `.env` step, and do not show the password on the sign-in page or skip sign-in (NFR-06, A-17). Counterbalances SM-1.

## 9. Open questions and assumptions

### Open questions

None block M0. One item depends on another module: the industry list in reference data (PLT.FR-10) is settled in the M1 module PRD (Q-16).

### Assumptions index

All accepted at the draft review on 2026-11-15.

| # | Where | Assumption |
|---|---|---|
| 1 | PLT.FR-1 | If the Emission Factors page is not built, its sidebar entry is hidden |
| 2 | PLT.FR-2 | On pages not tied to an organization, switching keeps the page |
| 3 | PLT.FR-2 | With no organizations, the switcher offers "Create organization" and organization pages lead to the list |
| 4 | PLT.FR-4 | A failed sign-in does not say whether the email or the password was wrong |
| 5 | PLT.FR-4 | Only in-app paths are accepted as the return page |
| 6 | PLT.FR-4 | Opening sign-in while signed in goes to `/` |
| 7 | PLT.FR-5 | An expired session sends the user to sign-in with a message, then back to the same page; unsaved input is lost |
| 8 | PLT.FR-6 | Demo account emails use the `example.com` domain |
| 9 | PLT.FR-9 | Health returns 200 when all is well, 503 when the database is unreachable |
| 10 | PLT.FR-11 | Demo data covers May 2025 to October 2026 |
| 11 | PLT.FR-11 | An existing demo organization is left untouched by the seed |
| 12 | PLT.FR-11 | Demo activity follows a believable monthly pattern |
| 13 | PLT.FR-12 | A missing `.env` stops the start with a message naming the file and the copy step |
| 14 | PLT.FR-13 | Error panels show the request ID |
