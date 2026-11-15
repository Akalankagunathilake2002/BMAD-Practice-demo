# Input reconciliation — CP4 UX

Inputs: the parent ticket `initiative-carbontrack` and its six child initiatives, the architecture spine, and the user's design brief (recorded in `.memlog.md`).

| Input item | Lands in |
|---|---|
| Brief: calm, professional, trustworthy; Stripe, Linear, Vercel | DESIGN Brand & Style; EXPERIENCE Inspiration |
| Brief: brand colours, Inter, logo, green not everywhere | DESIGN frontmatter, Colors, Typography, Brand & Style (logo A) |
| Brief: light default, dark optional | DESIGN -dark tokens; EXPERIENCE Foundation, avatar menu |
| Brief: sidebar labels | EXPERIENCE Information Architecture |
| Brief: cards, interactive charts, tables with search/filters/sorting/pagination | EXPERIENCE Component Patterns |
| Brief: feedback, loading/empty/success/error states, subtle animation | EXPERIENCE State Patterns, Interaction Primitives |
| Brief: responsive desktop/tablet/mobile, accessible for non-technical users | EXPERIENCE Responsive & Platform, Accessibility Floor |
| PLT R1–R5, R13, R14 (shell, sign-in, session, errors, page states) | EXPERIENCE IA, State Patterns |
| ORG R3, R4 (+ headline figures, EMI R13) | EXPERIENCE IA (Organizations) |
| ACT R1–R11 | EXPERIENCE Component Patterns (table, side panel, dialog, delete), States |
| EMI R3, R4, R7–R10 | EXPERIENCE Component Patterns (factor values, recalc banner, scope label) |
| DSH R1–R8 | EXPERIENCE Component Patterns (insight, overview, trend, period selector), Accessibility |
| RPT R1–R7 | EXPERIENCE IA, Reports exports |
| Architecture AD-9 (months), AD-14 (address, query keys, shared states, colour token names) | EXPERIENCE Trend chart, Period selector; DESIGN activity colours |

**Gaps found and fixed:** period selector (DSH R1), scope label (EMI R10, Could), Reports export behaviour (RPT R4–R6).

**Not in the UX (and why):** CSV column contents and file names (M5 addendum, not visual); API shapes (architecture).

**Requirement changes for Sofia:** PLT R1 sidebar labels (Organizations, Activity Data, Emissions); new M2 requirement for free-text search on descriptions.
