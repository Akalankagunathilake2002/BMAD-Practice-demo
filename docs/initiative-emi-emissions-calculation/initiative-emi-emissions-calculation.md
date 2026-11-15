---
tracker_id: ""
type: initiative
title: "Emissions calculation: every figure is explainable and never silently rewritten"
parent: none
sources:
  - docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/prd-emi-2026-11-14.md
  - docs/initiative-carbontrack/initiative-carbontrack.md
companions:
  - docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/addendum.md
after: []
risk: medium
---

# Emissions calculation: every figure is explainable and never silently rewritten

## Description

Every saved activity has a current calculation with its factor version kept, factors are versioned, and totals by type and month are available to other modules. Child 3 of the parent initiative `initiative-carbontrack`; its requirements come from the M3 module PRD.

## Outcome

Every reader can trace any emission figure to its activity, factor version and quantity, even after factors change.

## Requirements

- R1 (EMI.FR-1, MVP): Calculate on every save
- R2 (EMI.FR-2, MVP): Keep the calculation history
- R3 (EMI.FR-3, MVP): Return the factor version with every emission calculation
- R4 (EMI.FR-4, MVP): Keep the demonstration disclaimer attached
- R5 (EMI.FR-5, MVP): Total and share by activity type
- R6 (EMI.FR-6, MVP): Monthly totals
- R7 (EMI.FR-7, MVP): Add a factor version
- R8 (EMI.FR-8, Should): Emission Factors page
- R9 (EMI.FR-9, Should): Recalculate an organization
- R10 (EMI.FR-10, Could): Show the GHG scope label

Parent requirements this child covers: R23, R24, R25, R26, R27, R28, R29, R30, R31, R32, R33.

## Constraints

- The parent's constraints apply (docs/initiative-carbontrack/initiative-carbontrack.md, Constraints).
- Module PRD §0 names the master NFRs that matter most here.

## Done when

- SM-1: Exact totals.: The golden-dataset test (NFR-01) matches its expected totals by type and by month exactly.
- SM-2: Every number explainable.: 100% of the emission calculations returned by the API carry their factor version, and 100% of source labels contain the demonstration disclaimer.
- SM-3: Aggregates are fast.: The summary and monthly responses meet NFR-02.

## Boundaries

- Not this initiative: the module PRD's non-goals (§5) and the other modules' requirements.
- Waits on child 1 for the platform foundation and seed command (parent tickets.toml).

## References

- parent — docs/initiative-carbontrack/initiative-carbontrack.md, Requirements R23–R33
- prd — docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/prd-emi-2026-11-14.md, §4 Features
- addendum — docs/initiative-emi-emissions-calculation/prd-emi-2026-11-14/addendum.md, Architecture notes
- architecture — docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md, §11.1 Ownership
