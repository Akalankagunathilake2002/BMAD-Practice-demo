# PRD Quality Review — Module PRD: Platform Foundation (M0)

Reviewed: prd.md and addendum.md, draft of 2026-11-15. Stakes: internal demo, module PRD feeding UX (CP4) and stories (CP5).

## Overall verdict

**Good.** It is a clear capability spec for a platform module. Every master FR-PLT and FR-AUTH item maps to a testable PLT.FR, and the user's four decisions are reflected exactly. There are no critical or high findings. Four medium findings concern an unflagged inference, a missing consequence of the seed running on every start, a counter-metric aimed at a goal instead of a metric, and a trade-off hidden in the addendum.

## Decision-readiness — adequate

Decisions are stated plainly (fixed dates, calendar-year periods, landing page on switch, `.env` step).

### Findings
- **medium** The cost of fixed dates (the demo data ages, and the dashboard stops opening on the current year) appears only in the addendum (PLT.FR-11). *Fix:* name it in PLT.FR-11.

## Substance over theater — strong

Two personas, both of which drive requirements. There are no boilerplate NFRs; the master NFRs are cited.

## Strategic coherence — adequate

The thesis (a repeatable, professional frame that every module relies on) carries through.

### Findings
- **medium** SM-C1 counterbalances goal G1, not a success metric in this PRD (§8). *Fix:* aim it at SM-1: making the start easier must not bring back committed secrets or fallback values.

## Done-ness clarity — adequate

### Findings
- **medium** "The sign-in page and the not-found page do not use the app shell" (PLT.FR-1): the not-found part is an untagged inference, and architecture §10.2 makes not-found a signed-in page. *Fix:* keep the sign-in rule and leave not-found to UX.
- **medium** PLT.FR-11 does not say that the seed runs on every start (architecture §4). That is what makes idempotency matter to users. *Fix:* add "changes users make to demo data survive a restart".
- **low** "readable message" (PLT.FR-13) restates NFR-05's adjective. *Fix:* "plain-language message".

## Scope honesty — strong

Non-goals do real work (no server-side sign-out, no deployment). All 14 assumptions are tagged and indexed. There are no blocking open questions.

## Downstream usability — adequate

### Findings
- **low** "Session token" is used (PLT.FR-7, §5, §6) but not defined in the glossary. *Fix:* add it.

## Shape fit — strong

A capability spec suits a platform module. Adding new named journeys would be overhead.

## Mechanical notes

- The assumptions index matches the inline tags (14 = 14).
- IDs PLT.FR-1 to 14 are contiguous; SM-1 to 3 and SM-C1 are present.
- Input reconciliation added NFR-04 (shell keyboard access) and NFR-06 (CORS).
