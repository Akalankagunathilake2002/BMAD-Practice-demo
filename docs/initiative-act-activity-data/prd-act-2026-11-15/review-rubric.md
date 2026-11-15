# PRD Quality Review — Module PRD: Activity Data Management (M2)

Reviewed: prd.md and addendum.md, draft of 2026-11-15. Stakes: internal demo, module PRD feeding UX (CP4) and stories (CP5).

## Overall verdict

**Good.** The PRD has a clear thesis (entry is fast and hard to get wrong; M2 owns records, M3 owns numbers) and each of the 9 FRs has testable consequences. The user's three decisions are reflected exactly. There are no critical or high findings. Three medium findings: ACT.FR-7 is really an M1 behavior described from M2's side, the "no active factor" case is missing, and a display rule is not cited.

## Decision-readiness — strong

Decisions are stated and their trade-offs named in the addendum (re-link vs. refuse, keeping the quantity on a type change).

## Substance over theater — strong

Two personas, both of which drive requirements. No boilerplate.

## Strategic coherence — strong

SM-1 and SM-C1 pull against each other as intended.

## Done-ness clarity — adequate

### Findings
- **medium** ACT.FR-7 has one consequence that is an M1 behavior (the refusal message linking out) and one that says nothing happens. As written, an M2 story has nothing to build. *Fix:* reframe it as "the Activities page accepts a period and date-range filter from a link", which is what M2 builds; M1 owns the link.
- **medium** EMI.FR-1 rejects a save when the activity type has no active factor. ACT.FR-2 does not list that case. *Fix:* add it.
- **medium** Emissions display precision (kg CO2e to 2 decimals, master §6) is not cited. *Fix:* add to ACT.FR-5.
- **low** "a short confirmation" (ACT.FR-1) is vague. *Fix:* "a confirmation message that disappears on its own".

## Scope honesty — strong

Non-goals cover bulk import, choosing the period, unit conversion, totals, concurrency and undo. Fourteen assumptions for a UI-heavy module is reasonable, and all are indexed.

## Downstream usability — strong

Cross-references to EMI.FR, ORG.FR and PLT.FR IDs all resolve. Glossary terms are used consistently.

## Shape fit — strong

A capability spec with cited master journeys suits an internal data-entry module.

## Mechanical notes

- The assumptions index matches the inline tags (14 = 14).
- ACT.FR-1 to 9 are contiguous; SM-1 to 3 and SM-C1 are present.
