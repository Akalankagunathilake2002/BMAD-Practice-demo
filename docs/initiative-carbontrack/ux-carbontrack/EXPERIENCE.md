---
name: CarbonTrack
status: final
sources:
  - ../initiative-carbontrack.md
  - ../architecture-carbontrack/architecture-carbontrack.md
updated: 2026-11-15
---

# CarbonTrack — Experience Spine

## Foundation

Responsive web single-page app (React, TypeScript, Tailwind CSS v4, CarbonTrack's own UI kit, Recharts), desktop-first and usable from 360 px to 1920 px (NFR-03, A-15). English only, numbers formatted with `Intl` (A-11). `DESIGN.md` is the visual identity reference; this spine is the behaviour, and both win over the Figma frames ([Screens](https://www.figma.com/design/jM5YcK7kvfKwESlMbh9ZQh)). Requirements come from `initiative-carbontrack` and its six child initiatives; routes, page states, query keys and the selected-period rules come from the architecture spine (AD-14). Light theme is the default; dark is optional.

Primary user: **Sarah Williams**, Sustainability Manager at a medium-sized manufacturing company. Every demo account can do everything (roles are labels, A-01).

## Information Architecture

| Surface | Route | Reached from | Purpose |
|---|---|---|---|
| Sign in | `/login` | Any page when signed out | Demo sign-in, lists the demo accounts |
| Dashboard | `/organizations/:orgId/dashboard` | Sidebar "Dashboard", `/` | The footprint of the selected period in seconds |
| Organizations | `/organizations`, `/organizations/:orgId/settings` | Sidebar "Organizations", switcher | List, create, details (profile, periods, headline figures) |
| Activity Data | `/organizations/:orgId/activities` | Sidebar "Activity Data", insight banner, empty states | Record, review, correct activities; row side panel; add/edit dialog |
| Emissions | `/emission-factors` | Sidebar "Emissions" | Emission factors and their history, add a version, recalculate banner |
| Reports | `/organizations/:orgId/reports` | Sidebar "Reports" | Period summary, line items, CSV exports |
| Not found | `*` | Unknown addresses | Inside the shell, with a link to the dashboard `[ASSUMPTION]` |

Sidebar labels are **Dashboard · Organizations · Activity Data · Emissions · Reports** (user decision; same pages as PLT R1, "Emissions" opens the Emission Factors page). The top bar holds the organization switcher on the left and the user's name, role and avatar menu on the right; the avatar menu holds theme (Light / Dark) and Sign out `[ASSUMPTION]`.

Every stated need has a surface: record (Activity Data), see (Dashboard), explain (side panel history, Emissions), report (Reports), set up (Organizations). Dialogs stack one level deep at most.

## Voice and Tone

Plain, calm, specific — a colleague who knows the numbers, not a cheerleader.

| Do | Don't |
|---|---|
| "Electricity is your largest source: 62% of 2026 emissions" | "Great news! Here's your carbon data 🎉" |
| "Activity added · 2,736.00 kg CO2e" | "Success! Your activity was saved successfully." |
| "No activities in 2026 yet" | "Oops, nothing here!" |
| "This date is in the future. Activity dates can't be after today." | "Invalid date." |
| "Demonstration factors, not official values." | Any wording that implies official or audited figures |
| Units always: "kg CO2e", "t CO2e", "kWh", "L", "km" | Bare numbers |

## Component Patterns

| Component | Where | Behaviour |
|---|---|---|
| Insight banner | Dashboard | Names the largest activity type and its share of the selected period, with the figures; its button opens Activity Data filtered to that type and period. Hidden when the period has no activity `[ASSUMPTION]`. |
| Overview card | Dashboard | Total for the period with the previous-period change (DSH R8: amount, %, "favourable"/"unfavourable" in words and icon), donut and per-type bars with values and whole-percent shares. |
| Trend chart | Dashboard | Stacked monthly bars for every month of the period (empty months show an empty slot, AD-9); tooltips with exact value and unit on hover, focus or tap; legend items toggle series (DSH R7); a "Show as table" control for the data table (DSH R6). Axis in t CO2e, switching to kg when the largest month is under 1 t `[ASSUMPTION]`. |
| Activities table | Activity Data | Opens on the selected period (`period=all` available). One search box matches descriptions, case-insensitive, combined with the Type, Period and Dates filters; results update after typing pauses (~300 ms) `[ASSUMPTION]`. Sortable column headers (date default, newest first). Pagination 25 per page, up to 100. Quantities shown without trailing zeros with thousands separators `[ASSUMPTION]`; emissions in kg to 2 decimals, right-aligned. |
| Row side panel | Activity Data | Clicking a row (or Enter on a focused row) opens the panel: type, description, date and period; current calculation (emissions, quantity, factor value and version, source label, calculated time); calculation history newest first; Edit and Delete. The list stays visible and keeps its scroll; Esc or ✕ closes. |
| Add / edit dialog | Activity Data, empty states, dashboard (`?add=1`) | Type selector (three badges), quantity with the unit shown as a suffix, activity date with a calendar, optional description with a 0/500 counter. Under the date: "Linked automatically to reporting period {name}". Changing the type in edit shows "Unit changed from L to kWh. Check the quantity." under the quantity `[ASSUMPTION]`. Save keeps the dialog open on errors; errors appear under their field in the API's words. |
| Delete confirmation | Side panel | "Delete this activity? Its calculation history is removed too. This can't be undone." Cancel / Delete (danger) `[ASSUMPTION]`. |
| Recalculate banner | Emissions | When the active organization has out-of-date calculations: "{N} calculations use an older factor" with Recalculate; confirmation states the count; result "{N} calculations updated"; hidden when there are none. |
| Factor values | Emissions, side panel | Shown with as many decimals as they have, up to 6, without trailing zeros (0.48, 2.31) `[ASSUMPTION]`. |
| Toast | Global | Bottom-right, polite live region, dismisses after about 5 s; for completed actions only (added, saved, deleted, recalculated, exported). Errors stay where they happened `[ASSUMPTION]`. |
| Organization switcher | Top bar | Lists every organization by name; switching keeps the page and resets the period (PLT R2). |
| Period selector | Dashboard, Activity Data, Reports headers | A button showing the selected period ("2026 · Jan–Dec") with a menu of the organization's periods, newest first; the choice is shared by all three pages through the address (AD-14, DSH R1). |
| Reports exports | Reports | Two secondary buttons, "Export line items (CSV)" and "Export summary (CSV)", disabled with a reason when there are no line items; a toast confirms the download, an error keeps the page and offers Retry (RPT R4–R6). |
| Scope label (Could) | Emissions, Activity Data badge tooltip | "Scope 1/2/3 (informational)" as a caption next to the type on the Emissions page and in the type badge's tooltip (EMI R10) `[ASSUMPTION]`. |

## State Patterns

| State | Surface | Treatment |
|---|---|---|
| Loading | Every data view | Skeletons shaped like the content; cards and charts load independently (AD-14, DSH R5). |
| Empty — no activities | Activity Data, Dashboard, Reports | Empty card: mint icon circle, "No activities in {period} yet", one line of help, primary "Add activity". Dashboard keeps its cards at 0. |
| Empty — filters hide everything | Activity Data | "No activities match these filters" with "Clear filters" (M2 R4). |
| No periods | Dashboard, Reports | Empty state leading to adding a period (ORG R6). |
| Field error | Forms | Error text under the field in `{colors.status-danger}`, the field outlined in danger; focus moves to the first error on submit. |
| Load error | Any area | Error panel in that area only, with a plain message, the request ID as a small reference and Retry (PLT R13). |
| Success | After an action | Toast; the changed row is briefly highlighted in mint `[ASSUMPTION]`. |
| Out-of-date calculations | Emissions | Recalculate banner (above). |
| Session expired | Any | Back to sign-in with "Your session has expired", then to the same page (PLT R5). |

## Interaction Primitives

- **Mouse and keyboard parity.** Every action works by keyboard: Tab order follows reading order; Enter opens a focused row; Esc closes the topmost panel, dialog or menu and returns focus to where it came from.
- **One main action per view**, the primary button top right of the page header ("Add activity data", "Add activity").
- **Motion:** 150–200 ms ease-out for the side panel, dialogs, menus and toasts; chart transitions on period change only; nothing moves when the user prefers reduced motion `[ASSUMPTION]`.
- **Touch:** tap shows a chart tooltip, tap elsewhere hides it; no hover-only controls on touch.
- **Banned:** infinite scroll (pagination only), modal stacks deeper than one, autoplaying or decorative animation, confirmation dialogs for reversible actions.

## Accessibility Floor

WCAG 2.1 AA (NFR-04), behavioural part; contrast lives in `DESIGN.md`.

- Full keyboard operation with a visible 2 px focus ring in `{colors.focus-ring}`.
- Charts never rely on colour alone: legends name every type, badges carry the type name, and each chart has a keyboard-reachable table view and a screen-reader summary such as "Monthly emissions for 2026: total 98.60 t CO2e; electricity is the largest source at 62%" `[ASSUMPTION]`.
- Dialogs and the side panel trap focus while open and restore it on close; toasts and the recalculation result are announced politely.
- Form errors are linked to their fields and announced; required fields are marked in text, not only by colour.
- Reduced motion respected (above).

## Responsive & Platform

| Width | Behaviour |
|---|---|
| ≥ 1024 px | Sidebar visible; dashboard overview card is one row; side panel overlays the right 440 px. |
| < 1024 px | Sidebar becomes a drawer behind a menu button in the top bar; side panel becomes full width. |
| < 640 px | Table rows become stacked cards (date, type badge, emissions; tap for the side panel); dashboard cards and charts stack in one column; dialogs go full screen `[ASSUMPTION]`. |

Wide tables that still need columns scroll inside their container, never the page (NFR-03).

## Inspiration & Anti-patterns

- **Lifted from Stripe:** quiet cards with hairline borders, figures first, units muted, right-aligned tabular numbers.
- **Lifted from Linear:** a calm sidebar with clear active state, keyboard-first panels, no clutter.
- **Lifted from Vercel:** simple dashboards with one headline insight and strong type.
- **Rejected — an all-green interface:** green marks brand and action only.
- **Rejected — gamification** (badges, streaks, celebratory animations): this is a professional reporting tool.
- **Rejected — dense "student dashboard" grids** of many small charts: one insight, one overview, one trend.

## Key Flows

### Flow 1 — Sarah records October and sees what matters (realizes J2 and J3)

1. Sarah, Sustainability Manager at Greenfield Manufacturing, signs in on Monday morning with her demo account and lands on the Dashboard for 2026.
2. She clicks **Add activity data**; the Activity Data page opens with the dialog. She picks Electricity (the unit shows kWh), types 5,700, picks 31 Oct 2026 — "Linked automatically to reporting period 2026" — adds "Head office utility bill, October" and saves.
3. A toast confirms "Activity added · 2,736.00 kg CO2e" and the new row appears at the top, highlighted. She adds the October fuel and travel records the same way.
4. She returns to the Dashboard. The figures have updated without a refresh.
5. **Climax:** the insight banner reads **"Electricity is your largest source: 62% of 2026 emissions"**; the donut and bars confirm it at a glance. She clicks **View electricity activities** to see the bills behind the number.
6. She opens **Reports**, keeps 2026 selected and exports the line items and summary CSVs for her internal sustainability report.

Failure: she types a future date — "This date is in the future. Activity dates can't be after today." appears under the date and nothing is saved.

### Flow 2 — A factor changes and old figures catch up (realizes J4)

1. Sarah, acting as administrator, opens **Emissions** and adds electricity factor version 2 (0.48 kg CO2e per kWh) with a source note; the disclaimer is attached automatically.
2. New activities use version 2 at once. Existing ones do not change.
3. The page shows the banner "12 calculations use an older factor" with **Recalculate**. She confirms.
4. **Climax:** "12 calculations updated". Opening the October electricity row shows both calculations in its history — 2,850.00 kg with version 1 when added, 2,736.00 kg with version 2 now, marked current.

Failure: a recalculation error leaves every figure as it was (all-or-nothing) and offers Retry.