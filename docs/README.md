# CarbonTrack — Project Documentation

CarbonTrack is a mock carbon-management SaaS built to demonstrate an AI-assisted software development life cycle (AI SDLC) using the BMAD Method (v6.12.4, installed in `_bmad/`, with agents and workflows available as Claude Code skills in `.claude/skills/`). Every planning artifact lives in this folder, which is BMAD's output folder. Code is written only after the checkpoint that governs it has been approved.

> **Current stage:** CP1 and CP2 approved. Next: Phase 3 (Solutioning), starting with module PRDs for **Checkpoint 3**.

## Documents

| Phase | Document | Status |
|---|---|---|
| 1 · Analysis | [Project brief](analysis/project-brief.md) | v0.1 |
| 2 · Planning | [Master PRD](planning/prd.md) | **v0.2, approved (CP1)** |
| 2 · Planning | [Master Architecture](planning/architecture.md) (includes module dependencies and folder structure) | **v0.2, approved (CP2)** |
| 2 · Planning | [Assumptions & open questions](planning/assumptions-and-open-questions.md) | Living document |
| 2 · Planning | [BMAD decision trail](planning/.memlog.md) | Append-only log |
| 3 · Solutioning | Module PRDs: `planning/modules/` | Next (CP3) |
| 3 · Solutioning | UX design specification and design system: `planning/` | Not started (CP4) |
| 3 · Solutioning | Epics and stories: `planning/epics.md` | Not started (CP5) |
| 4 · Build | Sprint status and stories: `implementation/`. Source code: `backend/`, `frontend/` | Not started (CP6) |
| 5 · Course correction | Reviews, retrospectives, change proposals: `implementation/reviews/` | Not started |

## How each phase uses BMAD

| Phase | BMAD skill (agent) | Checkpoint |
|---|---|---|
| Analysis | `bmad-product-brief` (Mary, Analyst) | Fed into CP1 |
| Master PRD | `bmad-prd` (John, PM) | CP1 |
| Master Architecture | `bmad-architecture` (Winston, Architect) | CP2 |
| Module PRDs | `bmad-prd` (John, PM), one run per module, derived from the master PRD | CP3 |
| UX and design system | `bmad-ux` (Sally, UX Designer) | CP4 |
| Epics and stories | `bmad-create-epics-and-stories`, then the `bmad-sprint-planning` readiness check | CP5 |
| Build | `bmad-build` (Amelia, Dev) per story, then `bmad-code-review` | CP6 |
| Course correction | `bmad-review`, `bmad-correct-course`, `bmad-retrospective` | After CP6 |

The Phase 1 and 2 documents were written before BMAD was installed and were adopted as its planning artifacts at CP1 and CP2.

## Checkpoints and approval log

The workflow stops at each checkpoint until a human reviewer records a decision here. Each checkpoint's artifacts are delivered on their own branch and pull request (Q-17). Merging the PR is the GitHub record of approval.

| # | Checkpoint | Gates | Decision | Date | Reviewer | Notes |
|---|---|---|---|---|---|---|
| CP1 | Master PRD | Module-level planning | **Approved with changes** | 2026-11-10 | Akalanka | Q-02 changed to mock login and incorporated in PRD v0.2. Recommended options accepted for the other CP1 questions |
| CP2 | Master Architecture | Module-level planning | **Approved with changes** | 2026-11-10 | Akalanka | Q-08 stack additions approved. v0.2 adds mock login (§6.5) and the BMAD layout. Q-01: install BMAD. Q-17: PR per checkpoint |
| CP3 | Module PRDs | UX design | Not reached | | | |
| CP4 | UX & design | Epics and stories | Not reached | | | |
| CP5 | Epics & user stories | Build | Not reached | | | |
| CP6 | Development review | Course correction and release | Not reached | | | |

Possible decisions: **Approved**, **Approved with changes** (list the changes in Notes), or **Rejected** (rework required).

## Conventions

- Requirement IDs: `BR-nn` business, `FR-<MODULE>-nn` functional, `NFR-nn` non-functional. Stories and tests reference these IDs for traceability.
- Assumptions are `A-nn` and open questions are `Q-nn`. Both live in [assumptions-and-open-questions.md](planning/assumptions-and-open-questions.md).
- Each document carries a version and status in its header. When an approved document changes, its version is bumped and a change-log entry is added.
