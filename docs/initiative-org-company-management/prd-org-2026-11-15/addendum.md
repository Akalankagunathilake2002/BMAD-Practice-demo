# Addendum — Module PRD: Company Management (M1)

Detail that supports [prd.md](prd.md) but belongs in the architecture, UX or stories. The decisions themselves are recorded in [.memlog.md](.memlog.md).

## Options considered

| Topic | Chosen | Not chosen |
|---|---|---|
| Industry field (Q-16) | **Predefined list** (A-14, recommended at CP1) | Free text |
| Period edit that strands activities (ORG.FR-8) | **Refuse the edit** and say how many activities are affected | Move the activities to another covering period when one exists |
| Deleting periods | **Not allowed**: add and edit only, as in FR-ORG-05 | Allowed when the period has no activities |
| Headline figures scope (ORG.FR-5) | **All periods combined** | The current period; overall plus per-period figures |

**Why refuse rather than move.** Moving activities silently would change which period's totals they count in, and so change reports the user may already have exported. Refusing keeps every change visible and deliberate.

## Industry list rationale (ORG.FR-2)

Twelve broad values, so the dropdown stays short and every fictional demo organization fits one. "Other" keeps the list complete. The values are labels only: no factor or calculation depends on industry (A-08).

## Architecture notes

- **Period edit rule (ORG.FR-8).** No schema or endpoint change is needed, but the reporting-period service needs a rule. Before saving new dates, it counts the period's activities outside them, and refuses the edit with a new business-rule error (for example `REPORTING_PERIOD_EXCLUDES_ACTIVITIES`, 422) that carries the count. The composite foreign key in architecture §8.2 links activities to periods but does not check dates, so it cannot enforce this alone.
- **Case-insensitive period names (ORG.FR-7).** Architecture §8.2 has `UNIQUE (organization_id, name)`, which is case-sensitive. Assumption 6 needs a unique index on `(organization_id, lower(name))`, as organizations already have for `lower(name)`.
- **Naming the overlapping period (ORG.FR-7).** The no-overlap exclusion constraint refuses an overlapping period but cannot say which period it clashed with. The service should find the overlapping period first and return its name in the error, keeping the constraint as the final guarantee.
- **Headline figures (ORG.FR-5).** `GET /organizations/{orgId}` already returns them (architecture §9.2). They should come from the same query as M3's overall summary, so the numbers can never disagree.
