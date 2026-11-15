# Polish pass — structure and prose (bmad-review lenses)

This document exists to help the UX designer, architect and story writers act on M3's requirements without rereading the master PRD. Structure model: **Strategic/Context (Pyramid)**. Reader: humans. Style guide: Microsoft Writing Style Guide. Starting length: 2,484 words.

| Pass | Original Text | Revised Text | Changes |
|---|---|---|---|
| structure | Header table: no status or scope summary; scope only appears in §6 | MOVE: add Status and Scope rows to the header table | Puts the conclusion first, as the pyramid model needs (+25 words) |
| structure | §4.3 intro "M3 calculates the aggregates. M4 and M5 only display them." repeats §1 and §7 | PRESERVE | Short reinforcement at the point where readers of the aggregate FRs need it |
| structure | §7 "What M3 provides to other modules" | PRESERVE | Not template furniture: it is the hand-off contract for M2, M4 and M5 |
| prose | "(FR-EMI-xx, NFR-xx, G-x, J-x, Q-xx, A-xx)" | "(for example FR-EMI-01, NFR-02, G2, J4, Q-18, A-09)" | The master writes G2 and J4 without hyphens; real examples are clearer than patterns |
| prose | "Guesses not yet confirmed are marked `[ASSUMPTION]`" | "Rules inferred where the source documents are silent are marked `[ASSUMPTION]`… All of them were accepted…" | The sentence was no longer true once the assumptions were accepted |
| prose | EMI.FR-8: "A page, reached from the sidebar (FR-PLT-01), that shows factors…" | "The Emission Factors page, reached from the sidebar (FR-PLT-01), shows the factors…" | Sentence fragment made into a full sentence |

**Summary:** 6 recommendations (4 applied, 2 preserve). Net change about +70 words, for a final length of 2,553 words, which is within the internal-tool range. No comprehension trade-offs.

The addendum (about 400 words) was reviewed too and needed no changes.
