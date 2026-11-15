# Input reconciliation — master architecture v0.2

Input: [docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md](../../initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md), used for constraints only.

| Architecture item | Module PRD |
|---|---|
| §4 containers: migrations, then seed, then API; ports on localhost | PLT.FR-12 |
| §6.3 error envelope, §6.4 request ID | PLT.FR-13 |
| §6.5 mock authentication, 401 handling, client-only logout | PLT.FR-4, PLT.FR-5, PLT.FR-7; §5 |
| §8.3 idempotent seed | PLT.FR-11 |
| §10.2 routes and `/` redirect, §10.3 page states | PLT.FR-3, PLT.FR-14 |
| §12.1 configuration, CI (Q-12), §13 test database | Addendum, engineering setup |

**Gaps:**
1. The backend runs the seed command on **every** start (§4), not only the first. That makes "user changes to demo data survive a restart" an important consequence of idempotency. *Fixed* in PLT.FR-11.
2. §10.2 wraps every route except `/login` in the sign-in guard, so the not-found page is a signed-in page. The draft said it does not use the app shell, which was an unflagged inference. *Fixed:* removed; UX decides.
3. §9.2 does not define the health check's status code when the database is down. Covered by assumption 9 and the addendum.
