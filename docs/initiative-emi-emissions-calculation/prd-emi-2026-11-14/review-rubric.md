# PRD Quality Review — Module PRD: Emissions Calculation (M3)

Reviewed: prd.md and addendum.md, draft of 2026-11-14. Stakes: internal demo, module PRD feeding UX (CP4) and stories (CP5).

## Overall verdict

**Good.** The PRD has a clear thesis (every number is explainable, and a factor change never silently rewrites the past), and all ten FRs follow from it with testable consequences. There are no critical or high findings. Four medium findings concern vague wording, a missing value bound, glossary drift and an unstated schema need. Each has a direct fix.

## Decision-readiness — strong

Decisions are stated as decisions (Q-18 settled, edit rule, editable source text). The one real trade-off is named honestly, both in EMI.FR-1 and in the addendum: after a factor change, a description-only edit changes an activity's figure. Both open questions are genuinely open and have owners.

### Findings
- None.

## Substance over theater — strong

Only two personas, and both drive requirements (EMI.FR-7 to EMI.FR-9). The Vision is specific to this module. There are no copied NFRs; the master NFRs are referenced instead.

### Findings
- **low** SM-2 is closer to an acceptance check than a metric (§8). Acceptable for an internal demo. *Fix:* none needed.

## Strategic coherence — strong

Features follow the explainability thesis, and the MVP cut matches the master's MoSCoW priorities. SM-C1 (never trim history) correctly counterbalances SM-3 (speed).

### Findings
- None.

## Done-ness clarity — adequate

Most consequences are testable. Three need tightening.

### Findings
- **medium** "rejected with a clear error" (EMI.FR-1) uses an adjective, not a condition. *Fix:* say the error names the activity type that has no active factor, and that it uses the standard error envelope (NFR-05).
- **medium** EMI.FR-7 gives a lower bound for factor values but no upper bound. *Fix:* add the maximum that follows from architecture §8.2 (999,999.999999).
- **low** EMI.FR-6 only implies that a reporting period is required. *Fix:* state it.

## Scope honesty — strong

Non-goals do real work: no auto-recalculation, no regional factors, no display ownership. All 11 assumptions are tagged inline and indexed, and that count is fine for an internal demo.

### Findings
- None.

## Downstream usability — adequate

The glossary is present and IDs are contiguous (EMI.FR-1 to 10, SM-1 to 3, SM-C1).

### Findings
- **medium** Glossary drift: "result" is used where the glossary term is "emission calculation" (§1, §4.2 heading, EMI.FR-1, EMI.FR-3 title). *Fix:* use the glossary terms.
- **medium** EMI.FR-8 needs the date each factor version was added, but architecture §8.2 gives immutable tables like `emission_factors` no timestamp. *Fix:* add this to the addendum's architecture impacts and to open question 1.

## Shape fit — strong

A capability-spec shape that cites the master journeys suits an internal module PRD. Adding new named-persona journeys would be overhead.

## Mechanical notes

- The assumptions index matches the inline tags (11 = 11).
- Cross-references to §4.3 and §9 resolve.
- The master NFRs are not stated to apply (raised by input reconciliation; fixed in §0).
- EMI.FR-10 "marked as informational" leaves the wording to UX. Acceptable.
