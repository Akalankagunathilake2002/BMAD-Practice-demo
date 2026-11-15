---
type: migration
title: "Migrate CarbonTrack planning from BMAD v6 to the v7 initiative layout"
status: draft
created: 2026-11-15
---

# Migration plan: v6 → v7 (method module, migration-1)

Nothing below happens until this plan is approved. Every move is a `git mv`, so history follows each file. Nothing is deleted except folders emptied by the moves. Nothing is pushed.

## Answers

| Question | Answer |
|---|---|
| Planning content | `docs/planning/`: master PRD, master architecture, assumptions log, planning decision log, and six module PRD run folders under `prds/` |
| Implementation content | `docs/implementation/` does not exist, so there is nothing to convert: no `epics.md`, `sprint-status.yaml`, story files or specs |
| Anything elsewhere | `docs/analysis/project-brief.md` moves in (the master PRD's source). `docs/README.md` stays where it is |
| Grouping | **One initiative per module.** In v7 terms: a parent initiative `initiative-carbontrack` for the master documents, whose `tickets/tickets.toml` lists six child initiatives in build order (bmad-agent-po `slice.md`, "parent initiative") |
| Workspace | No. The store stays at `docs/` in this repository |
| Stories in progress | None |
| Older document versions to archive | None. No two folders differ only by date |

Source folders were read from the backup's `_bmad/config.toml`, because `bmad setup` had already removed the keys: `output_folder = docs`, `planning_artifacts = docs/planning`, `implementation_artifacts = docs/implementation`.

**Backup:** `.v6-v7-migration-backup-20261115-1430/` holds `_bmad/` and all of `docs/` (54 files). It has a `.gitignore` of `*` and is never committed.

## Target tree

```
docs/
  README.md                                    stays; links rewritten
  initiative-carbontrack/                      ← docs/planning/ renamed (parent initiative)
    initiative-carbontrack.md                  new envelope from the master PRD
    .memlog.md                                 planning decision log, unchanged
    assumptions-and-open-questions.md          unchanged (not a recognized document type, goes along)
    prd-carbontrack/prd-carbontrack.md         ← planning/prd.md
    architecture-carbontrack/architecture-carbontrack.md   ← planning/architecture.md
    project-brief/project-brief.md             ← analysis/project-brief.md
    tickets/tickets.toml                       six [[initiative]] tables, in build order
  initiative-plt-platform-foundation/          child 1 (M0)
    initiative-plt-platform-foundation.md      new envelope from the M0 PRD
    prd-plt-2026-11-14/                         ← planning/prds/prd-plt-2026-11-14/, moved whole
      prd-plt-2026-11-14.md                     ← prd.md, renamed to match its folder
      addendum.md, .memlog.md, reconcile-*.md, review-*.md   unchanged names
  initiative-org-company-management/           child 2 (M1), same shape, prd-org-2026-11-15/
  initiative-emi-emissions-calculation/        child 3 (M3), same shape, prd-emi-2026-11-14/
  initiative-act-activity-data/                child 4 (M2), same shape, prd-act-2026-11-15/
  initiative-dsh-dashboard/                    child 5 (M4), same shape, prd-dsh-2026-11-15/
  initiative-rpt-reports/                      child 6 (M5), same shape, prd-rpt-2026-11-15/
  backlog/.gitkeep                             always created
  archive/.gitkeep                             always created
  migration-v6-v7/migration-v6-v7.md           this plan and its verification record
```

Removed once empty: `docs/planning/prds/` (inside the renamed folder) and `docs/analysis/`.

No `archive-v6/` is created: there are no v6 tracking sources to archive.

## Child initiatives (parent `tickets/tickets.toml`)

Build order follows architecture §11.3 (M0 → M1 → M3 → M2 → M4/M5).

| id | Child folder | Module | `covers` (parent R ids) | `after` |
|---|---|---|---|---|
| 1 | `initiative-plt-platform-foundation` | M0 | the FR-PLT and FR-AUTH lines | none |
| 2 | `initiative-org-company-management` | M1 | the FR-ORG lines | 1: shell, sign-in, page states |
| 3 | `initiative-emi-emissions-calculation` | M3 | the FR-EMI lines | 1: platform foundation |
| 4 | `initiative-act-activity-data` | M2 | the FR-ACT lines | 2: periods; 3: calculation on save |
| 5 | `initiative-dsh-dashboard` | M4 | the FR-DSH lines | 2: periods; 3: aggregates |
| 6 | `initiative-rpt-reports` | M5 | the FR-RPT lines | 3: aggregates; 4: activity records |

Children get no `tickets/` folder yet. Sofia (`bmad-agent-po`) slices each into epics when you plan it.

## Envelopes (written from bmad-agent-po's ticket template)

- **Parent `initiative-carbontrack.md`:** `type: initiative`, `parent: none`. Description and Outcome from the master PRD §1. Requirements: one line per master requirement in this release, `R<n> (FR-xxx-nn): <the PRD's line>`, 45 lines, with the four Won't items (FR-AUTH-06, FR-ORG-06, FR-ACT-07, FR-RPT-06) under Boundaries instead. Constraints from master §6 (calculation rules) and the NFRs. `Done when` from goals G1–G5. `sources:` the master PRD and the brief; `companions:` the architecture and the assumptions log.
- **Each child `initiative-<slug>.md`:** `type: initiative`, `parent: none` (the template's rule for initiatives), `sources:` its module PRD and the parent's envelope (v7: "each child names the parent's file in `sources:`"). Requirements: one line per module FR, `R<n> (PLT.FR-1): <the FR's title>`. `Done when` from the module PRD's success metrics. Boundaries from its non-goals.
- **Main files renamed** gain `type`, `title` and `created` frontmatter where missing: the master PRD and the architecture have none today; the module PRDs gain `type: prd`.

## Path rewrites

Inside `docs/` (live files only; decision logs stay unchanged because they are append-only):
- Relative links in every moved file are recomputed from its new folder: the module PRDs' links to the master PRD, the architecture, the brief and to each other, the addenda's `prd.md` links, and the `reconcile-*.md` and `review-*.md` links.
- The module PRDs' frontmatter `parent: docs/planning/prd.md` points to the master PRD's new path.
- `docs/README.md`: the five links to `analysis/…` and `planning/…`.

Outside `docs/` and `_bmad/` (reported, never edited):
- `PROGRESS.md` (your untracked local notes): mentions `docs/planning/…` paths.

## Configuration

- `active_initiative = "initiative-carbontrack"` under `[core]` in `_bmad/custom/config.user.toml`, so the parent shows the whole plan. Switch to a child when you work on it (`bmad`, "switch initiative").
- No `_bmad/custom/` override names a moved path, and no other module config points inside `docs/planning/`.
- `planning_artifacts` and `implementation_artifacts` are already gone (removed by `bmad setup`).

## Commits

The repository already exists, so the current state was committed first (`1c7267f`). After that, one commit per step that changes something: documents, envelopes and ticket tree, path rewrites, configuration and cleanup. The backup is never staged, and nothing is pushed.

## What stays in place

| Path | Why |
|---|---|
| `docs/README.md` | Store root file: the docs index and checkpoint approval log. Its links are updated |
| `PROGRESS.md` | Outside the store; untracked local notes |

## Verification

*Filled in after the moves (step 15).*
