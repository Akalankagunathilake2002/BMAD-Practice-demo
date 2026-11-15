# PRD Quality Review — Module PRD: Dashboard (M4)

Reviewed: prd.md and addendum.md, draft of 2026-11-15. Stakes: internal demo, module PRD feeding UX (CP4) and stories (CP5).

## Overall verdict

**Good, after one high fix.** The PRD has a clear thesis (understand it in ten seconds; every number from M3) and testable FRs, and the user's three decisions are reflected exactly. One high finding: the chart text equivalent was tagged Should although NFR-04 requires it, which would let it drop out of the MVP. Two medium findings (missing disclaimer, wrong owner for chart components) and one low (restating FR-DSH-01) are fixed.

## Decision-readiness — strong

The trade-off of showing future months as zero is named in the addendum.

## Substance over theater — strong

Two personas, both of which drive requirements.

## Strategic coherence — strong

SM-2 (one source of numbers) tests the thesis directly, and SM-C1 keeps SM-1 honest.

## Done-ness clarity — adequate

### Findings
- **high** DSH.FR-6 is tagged Should, but it implements NFR-04's "text or table equivalent", and master NFRs apply to the MVP. The §6 sentence argues for keeping it separate but still lets it be dropped. *Fix:* tag it MVP, update the header and §6, and keep it as its own FR so it gets its own story.
- **medium** The dashboard shows emissions, so FR-EMI-09's disclaimer applies, and no FR says so. *Fix:* add to DSH.FR-2.
- **low** DSH.FR-2's first bullet lists the four cards, restating FR-DSH-01. *Fix:* cite it.

## Scope honesty — strong

Non-goals cover aggregation, all-time figures, filtering, export and targets. Eleven assumptions, all indexed.

## Downstream usability — adequate

### Findings
- **medium** §7 says the chart components belong to M0, but architecture §11.1 assigns `components/charts/` to M4. *Fix:* correct §7.

## Shape fit — strong

## Mechanical notes

- The assumptions index matches the inline tags (11 = 11).
- DSH.FR-1 to 8 are contiguous; SM-1 to 3 and SM-C1 are present.
