# Input reconciliation — master PRD v0.2

Input: [docs/planning/prd.md](../../prd.md), compared with prd.md and addendum.md.

| Master item | Module PRD |
|---|---|
| FR-PLT-01 | PLT.FR-1 |
| FR-PLT-02 | PLT.FR-8 |
| FR-PLT-03 | PLT.FR-9 |
| FR-PLT-04 | PLT.FR-11, PLT.FR-6 |
| FR-PLT-05 | PLT.FR-12 |
| FR-PLT-06 | PLT.FR-10 |
| FR-AUTH-01, FR-AUTH-02 | PLT.FR-4 |
| FR-AUTH-03 | PLT.FR-5 |
| FR-AUTH-04 | PLT.FR-6 |
| FR-AUTH-05 | PLT.FR-7 |
| FR-AUTH-06 (Won't) | §5 non-goals |
| NFR-05 | PLT.FR-13 |
| G3, G4, MVP acceptance criterion 3 | PLT.FR-14; PLT.FR-12; SM-1 |

**Gaps:**
1. NFR-04 (keyboard operation, visible focus) is not mentioned for the shell, drawer and switcher, which M0 builds first. *Fixed* in PLT.FR-1.
2. NFR-06's CORS rule (only configured origins) is not mentioned, and M0 configures it. *Fixed* in PLT.FR-12.
