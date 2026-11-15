# Review — Versions and reality check

- **Spine:** `docs/initiative-carbontrack/architecture-carbontrack/architecture-carbontrack.md` (status draft, updated 2026-11-15)
- **Lens:** were the committed versions and technologies checked against live sources rather than asserted? Do the packages fit together (peer ranges, engines, Python requirements)? What does the live starter do by default? Is anything the stack needs missing?
- **Reviewer:** independent; did not write the spine. Review only, so the spine was not edited.
- **Checked on:** 2026-11-15, against the npm registry, PyPI, endoflife.date, Docker Hub, GitHub (docker-library Dockerfiles, actions releases) and the published package contents (wheels and tarballs unpacked in a scratch directory). It also ran bcrypt 5.0.0 and PyJWT 2.15.1 in a throwaway venv.

## Verdict

Every version number in the Stack table matches the live registries, and the TypeScript 6.0 pin holds up. The spine checked each package on its own, though, not how the packages fit together. It leaves out the Node.js runtime and the test-DOM packages the frontend toolchain needs. It does not deal with the live Vite starter, which now scaffolds TypeScript 7 and oxlint. It also commits `httpx`, which FastAPI's own TestClient now deprecates. Fix these before M0 scaffolding starts.

## Findings at a glance

| # | Severity | Finding | Spine location |
| --- | --- | --- | --- |
| F1 | high | No Node.js version, although the chosen packages need Node ≥ 22.22.2 / 24.15 / 26. The author's own machine (Node 22.18.0) fails these engine ranges | Stack table; Structural Seed (frontend); Quality gates |
| F2 | high | The live `create-vite` react-ts starter ships TypeScript `~7.0.2` and oxlint with no ESLint. Adding typescript-eslint 8.72 on top of it is a peer conflict | Stack: TypeScript 6.0, ESLint / typescript-eslint row; Deferred: TypeScript 7 |
| F3 | high | `httpx` 0.28 is deprecated for FastAPI's TestClient (Starlette 1.10 asks for `httpx2`), and the deprecation warning shows by default | Stack: pytest / httpx / ruff row; Testing convention |
| F4 | medium | Vitest has no DOM environment (jsdom/happy-dom are optional peers and are not installed). React Testing Library's required peer `@testing-library/dom` is not named | Stack: Vitest / RTL / MSW row |
| F5 | medium | By default the Vite dev server binds to `localhost` and uvicorn to `127.0.0.1`. Inside containers, Compose's published ports can't reach them. The CORS origin also differs from the URL the spine shows | Structural Seed; AD-16 |
| F6 | medium | bcrypt 5.0 raises `ValueError` on passwords over 72 bytes, including in `checkpw`. Sign-in will return a 500 unless this is handled | AD-13; Stack: PyJWT / bcrypt row |
| F7 | medium | The CI fit is unchecked: no action versions, no Node/Python setup, and no PostgreSQL for the "real PostgreSQL" tests | Consistency Conventions: Quality gates, Testing |
| F8 | low | PyJWT 2.15 requires a string `sub` and warns on HMAC keys under 32 bytes | AD-13; AD-16 |
| F9 | low | Python 3.12 has been security-only since 2025-04-02. Nothing records why it was chosen over 3.14 | Stack: Python row |
| F10 | low | Supporting pieces are missing: `react-is` (a required Recharts peer), `pytest-cov` for the 80% gate, the ESLint flat-config plugins, `psycopg[binary]`, `@types/react*`, base images | Stack table |
| F11 | low | React Router 8 is published only as `react-router`. `react-router-dom` stops at 7.18.5 | Stack: React Router row |
| F12 | low | The verification line gives no evidence (peer ranges, engines, exact patches). Fast-moving 0.x packages are pinned only to minor versions | Stack header line |

---

## Findings

### F1 — Node.js runtime is not in the stack (high)

- **Location:** Stack table (lines 176–197) has no Node.js row. Structural Seed shows a `frontend` "Vite dev server" container with no base image. Quality gates say GitHub Actions lints and tests on every push, but name no Node version.
- **What I checked:** the `engines` of every frontend package in the Stack plus the test-DOM packages it needs (F4); endoflife.date for Node.js; Docker Hub `node` tags; the local machine's Node.
- **Evidence:**
  - npm `engines.node`: vite 8.3.8 `^20.19.0 || >=22.12.0`; **vitest 5.1.0 `^22.12.0 || ^24.0.0 || >=26.0.0`**; **jsdom 31.0.1 `^22.22.2 || ^24.15.0 || >=26.0.0`**; **react-router 8.5.1 `>=22.22.0`**; msw 3.0.6 `>=22.12.0`; eslint 10.15.0 `^20.19.0 || ^22.13.0 || >=24`; @testing-library/jest-dom 7.0.1 `>=22`.
  - The effective floor is Node 22.22.2+, 24.15+, or 26+.
  - endoflife.date `nodejs`:
    - 22: maintenance, EOL **2027-04-30**
    - 24: active support ended 2026-10-20, EOL 2028-04-30, latest 24.22.3
    - 26: LTS since 2026-10-28, active support until 2027-10-27, EOL **2029-04-30**, latest 26.13.1
  - Docker Hub: `node:26-alpine` and `node:24-alpine` were both updated 2026-11-11.
  - `node --version` on the project machine prints **v22.18.0**. That fails react-router 8.5.1 and jsdom 31, so host-side `npm test` / lint would not work as committed.
- **Fix:**
  - Add a Stack row: **Node.js 26 (LTS)**. It is in active LTS and every package above accepts `>=26`. Node 24 (≥ 24.15) is the conservative alternative.
  - Commit `.nvmrc` (`26`) and `"engines": {"node": ">=26"}` in `frontend/package.json`.
  - Use `node:26-alpine` for the frontend image and `actions/setup-node` with `node-version-file: frontend/.nvmrc` in CI.
  - Pin `@types/node` to the same major.

### F2 — The live Vite starter contradicts the TypeScript 6.0 + ESLint decision (high)

- **Location:** Stack rows "TypeScript 6.0" and "ESLint / typescript-eslint / Prettier"; Consistency Conventions "Quality gates"; Deferred "TypeScript 7". Neither the spine nor the detail document names how `frontend/` is scaffolded.
- **What I checked:** the TypeScript-6 claim itself, plus the defaults of the starter a Vite + React + TS greenfield project uses (`npm create vite@latest -- --template react-ts`).
- **Evidence — the claim holds:**
  - typescript-eslint `latest` 8.72.2 (2026-11-09) and `canary` 8.72.3-alpha.8 both have peer `typescript: ">=4.8.4 <6.1.0"`. No 9.x exists on npm.
  - TypeScript `latest` is 7.0.2 (2026-07-08) and `next` is 7.1.0-dev.20261115.1.
  - NFR-07 (`prd-carbontrack.md` line 173) names ESLint, so the pin is justified.
  - Side note: 6.0.3 (2026-04-16) is the last 6.x release, so the pin means no TypeScript updates until typescript-eslint moves.
- **Evidence — the starter disagrees:** `create-vite` latest is **9.3.4** (published 2026-11-05). Its `template-react-ts/package.json` declares:
  - `"typescript": "~7.0.2"`, `"oxlint": "^1.90.0"`, `"lint": "oxlint"`
  - no eslint, no prettier, no vitest
  - `vite ^8.3.6`, `@vitejs/plugin-react ^6.1.2`, `@types/node ^24.19.2`, plus a `.oxlintrc.json`

  Its README recommends `oxlint-tsgolint` for type-aware rules. oxlint 1.93.0 has peer `oxlint-tsgolint >=7.0.2005`, which is aligned with TS 7, not 6. Running `npm i -D typescript-eslint@8.72` in the scaffolded app fails npm's peer resolution (typescript 7.0.2 is outside `<6.1.0`).
- **Fix:** add a "Frontend scaffold" line to the Structural Seed (or the Stack):
  1. `npm create vite@9.3.4 frontend -- --template react-ts`.
  2. Set `typescript` to `~6.0.3`.
  3. Delete `oxlint` and `.oxlintrc.json`.
  4. Add eslint 10.15, @eslint/js 10.0, typescript-eslint 8.72, eslint-plugin-react-hooks 7.1, eslint-plugin-react-refresh 0.5, globals 17, eslint-config-prettier 10.1 and prettier 3.10. All of these declare peers that accept ESLint 10 (checked on npm).
  5. Keep `"strict": true` explicit in `tsconfig.app.json`. TS 6.0.3 already defaults strict on (`getStrictOptionValue` returns `compilerOptions.strict !== false`), but the starter's tsconfig omits it and the convention should not rely on a default.

  Alternatively, state that the frontend is assembled by hand and list its config files. Either way, the Deferred "TypeScript 7" entry should also say that a move to oxlint + tsgolint would need NFR-07 to change.

### F3 — `httpx` is the deprecated TestClient transport; FastAPI's stack now uses `httpx2` (high)

- **Location:** Stack row "pytest / httpx / ruff — 9.2 / 0.28 / 0.17"; Testing convention ("service and API tests"); detail §3 "pytest, httpx (FastAPI TestClient)".
- **What I checked:** FastAPI 0.163.1's dependencies, then the `starlette.testclient` source inside the latest Starlette wheel.
- **Evidence:**
  - FastAPI 0.163.1 (published 2026-11-15) requires `starlette>=0.46.0`, which resolves to **Starlette 1.10.0**. Its `[standard]` extra lists `httpx2>=2.0.0`.
  - `starlette/testclient.py` in 1.10.0 runs `import httpx2 as httpx`. On `ModuleNotFoundError` it falls back to `httpx` and emits `StarletteDeprecationWarning("Using `httpx` with `starlette.testclient` is deprecated; install `httpx2` instead.")`.
  - `StarletteDeprecationWarning` subclasses `UserWarning` so that it is "visible by default" (`starlette/exceptions.py`).
  - `starlette[full]` lists `httpx2>=2.0.0` and `httpx<0.29.0,>=0.27.0`.
  - PyPI: httpx latest stable is still **0.28.1, uploaded 2024-12-06**; only `1.0.dev*` pre-releases have followed. **httpx2 2.16.0** (pydantic/httpx2, 2026-10-21) needs Python ≥ 3.11.
  - Impact: every API test module prints the warning. If pytest is set to `filterwarnings = error`, a normal choice for a project with enforced quality gates, every API test fails at import. The fallback will also go away in a future Starlette release.
- **Fix:** change the row to **pytest / httpx2 / ruff — 9.2 / 2.16 / 0.17** and depend on `httpx2` in the backend dev dependencies. Record Starlette 1.10 as the transitive pin under FastAPI 0.163.

### F4 — Vitest DOM environment and React Testing Library's DOM peer are missing (medium)

- **Location:** Stack row "Vitest / React Testing Library / MSW — 5.1 / 16.3 / 3.0". The Testing convention and detail §13 require page-state tests and ActivityForm validation tests.
- **What I checked:** peer dependencies and `peerDependenciesMeta`.
- **Evidence:**
  - vitest 5.1.0 marks `jsdom` and `happy-dom` as **optional** peers, so neither is installed automatically. `environment: 'jsdom'` fails without jsdom installed.
  - @testing-library/react 16.3.3 has peer `@testing-library/dom: ^10.0.0` (required; latest 10.4.2) and does not ship the DOM queries itself.
  - Current versions: jsdom 31.0.1 (Node `^22.22.2 || ^24.15.0 || >=26.0.0`, see F1); happy-dom 21.0.4 (Node ≥ 20); @testing-library/jest-dom 7.0.1 (peer `vitest >=0.32`, Node ≥ 22); @testing-library/user-event 14.6.10.
  - MSW 3.0.6 still exports `./node` (for `setupServer` in Vitest). Its `typescript >=5.9.x` peer is optional and accepts 6.0.
- **Fix:** add Stack rows for **jsdom 31** (or happy-dom 21; pick one), **@testing-library/dom 10.4**, **@testing-library/jest-dom 7.0** and **@testing-library/user-event 14.6**. Also add a convention line: `test.environment = 'jsdom'`, and a `src/test/setup.ts` that starts the MSW server and loads jest-dom matchers.

### F5 — Dev servers bind to loopback inside their containers (medium)

- **Location:** Structural Seed deployment diagram (`127.0.0.1:5173` → "Vite dev server", `127.0.0.1:8000` → "FastAPI + uvicorn"); AD-16 ("Compose binds every port to `127.0.0.1`"). Detail §4 starts the backend with plain `uvicorn --reload`.
- **What I checked:** the live default bind address of both servers.
- **Evidence:**
  - Vite 8.3.8 `dist/node/chunks/node.js` `resolveHostname`: an unset `server.host` becomes `"localhost"`. The starter's `vite.config.ts` sets no `server.host`.
  - uvicorn 0.54.0 `config.py` sets `host: str = "127.0.0.1"`, and the CLI default in `main.py` is `"127.0.0.1"`.
  - A process bound to the container's loopback cannot be reached through a published port, so both services would refuse connections from the host.
  - Separately, detail §12.1's `CORS_ORIGINS` example is `http://localhost:5173`, but the spine's diagram has the browser on `127.0.0.1:5173`. Those are different origins, so CORS would block a user who follows the diagram.
- **Fix:** add to AD-16 (or the Structural Seed):
  - Inside containers, servers bind `0.0.0.0`: `uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload`, and `vite --host 0.0.0.0 --port 5173 --strictPort` or `server.host: true`.
  - Compose alone restricts exposure with `"127.0.0.1:5173:5173"` / `"127.0.0.1:8000:8000"`.
  - Pick one canonical browser URL (`http://localhost:5173`) and use it in the diagram, `CORS_ORIGINS` and `VITE_API_BASE_URL`, or allow both origins.

### F6 — bcrypt 5.0 raises on passwords longer than 72 bytes (medium)

- **Location:** AD-13 ("Seeded users with bcrypt hashes sign in…"); Stack row "PyJWT / bcrypt — 2.15 / 5.0"; Errors convention ("no stack trace … reaches a client").
- **What I checked:** the bcrypt 5.0.0 changelog in the wheel's METADATA, plus a live run in a scratch venv.
- **Evidence:**
  - Changelog: "Passing `hashpw` a password longer than 72 bytes now raises a `ValueError`. Previously the password was silently truncated."
  - Live: `bcrypt.checkpw(b'a'*72, h)` → `False`; `bcrypt.checkpw(b'a'*73, h)` → **`ValueError: password cannot be longer than 72 bytes`**.
  - Unhandled, a long password at `POST /auth/login` becomes a 500 instead of the 401 invalid-credentials envelope.
  - Also relevant: passlib's latest release is 1.7.4 (2020-10-08) and it is not compatible with modern bcrypt. The spine correctly names `bcrypt` directly, and should keep doing so.
- **Fix:**
  - In AD-13 or the sign-in schema, cap the password at 72 UTF-8 bytes. Over the cap, return the same 401 as a wrong password, or a 422 validation error; decide which.
  - Validate `DEMO_USER_PASSWORD` at seed time against the same limit.
  - Add a convention: "hash with `bcrypt` directly; never passlib".

### F7 — GitHub Actions is named, but its fit with the test strategy was not checked (medium)

- **Location:** Consistency Conventions "Quality gates" (GitHub Actions on every push) and "Testing" (service and API tests "against real PostgreSQL"). Detail §13 says the test database uses "the compose PostgreSQL instance".
- **What I checked:** current action majors and runtimes; whether CI has a PostgreSQL.
- **Evidence:**
  - GitHub releases: `actions/checkout` v7.0.1 (2026-07-20), `actions/setup-node` v7.1.0 (2026-10-08), `actions/setup-python` v7.1.0 (2026-10-22). All declare `using: node24` in `action.yml`.
  - A GitHub-hosted runner has no Compose stack running, so the "compose PostgreSQL instance" does not exist in CI unless the workflow starts one.
  - The test suite needs PostgreSQL 16 with `btree_gist` (AD-8's exclusion constraint).
- **Fix:** add a CI line to the Structural Seed:
  - job `services.postgres` using `postgres:16-alpine` with a `pg_isready` health check (or `docker compose up -d db`)
  - `actions/setup-python@v7` with the chosen Python
  - `actions/setup-node@v7` with `node-version-file`
  - lint, then tests with coverage (see F10 for `pytest-cov`)

### F8 — PyJWT 2.15 behaviours AD-13/AD-16 depend on (low)

- **Location:** AD-13 (signed bearer token); AD-16 (secrets from the environment); detail §6.5 (claims `sub, name, role, exp`).
- **Evidence (live run, PyJWT 2.15.1):**
  - Encoding `{'sub': uuid4()}` raises `TypeError: Object of type UUID is not JSON serializable`.
  - Decoding a token with an integer `sub` raises `InvalidSubjectError: Subject must be a string`, because `verify_sub` defaults to True.
  - Encoding with a 9-byte key emits `InsecureKeyLengthWarning` ("below the minimum recommended length of 32 bytes for SHA256"), from `HMACAlgorithm.check_key_length`.
  - `enforce_minimum_key_length=True` turns that warning into an `InvalidKeyError`.
- **Fix:**
  - Put `sub = str(user.id)` in the convention.
  - Have pydantic-settings reject an `AUTH_SECRET_KEY` shorter than 32 bytes at startup. The current placeholder `change-me-to-a-long-random-string` is 33 bytes and passes; test fixtures must also use secrets of 32 bytes or more.
  - Optionally decode with `options={"enforce_minimum_key_length": True}`.

### F9 — Python 3.12 is past bugfix support (low)

- **Location:** Stack row "Python 3.12". The brief (`project-brief.md` line 67) fixes FastAPI/Pydantic but no Python version, so 3.12 is a CP2 choice.
- **Evidence:**
  - endoflife.date `python`: 3.12 bugfix support ended **2025-04-02** (security-only, source-only releases), EOL 2028-10-31, latest 3.12.16. 3.14 has bugfix support until 2027-10-01 and EOL 2030-10-31.
  - Every compiled dependency already ships cp314 wheels: pydantic-core 2.50.4, psycopg-binary 3.3.9, SQLAlchemy 2.1.8; bcrypt 5.0.0 is abi3 cp38+.
  - FastAPI 0.163, pydantic-settings 2.16, SQLAlchemy 2.1, Starlette 1.10 and httpx2 all require ≥ 3.11.
  - Docker Hub `python:3.12-slim` was updated 2026-11-08, so the image is fine.
- **Fix:** either record "3.12, security-only, EOL 2028-10-31, accepted for a local demo", or move to **Python 3.14** (`python:3.14-slim`) at no compatibility cost. Add `requires-python` to `pyproject.toml` either way.

### F10 — Supporting packages and images missing from the Stack (low)

- **Location:** Stack table.
- **Evidence and fixes:**
  - **`react-is`:** recharts 3.10.1 declares `react-is` as a required peer, with no `peerDependenciesMeta`. Pin `react-is 19.3` to match React 19.3.1 so a package manager that does not auto-install peers does not drift.
  - **Coverage tool:** the Testing convention demands at least 80% line coverage on `services/` and `calculation/`, but no tool is named. Add `pytest-cov 7.1` (PyPI 7.1.0) with `--cov-fail-under=80` scoped to those packages.
  - **ESLint flat-config companions** (see F2): @eslint/js 10.0.1, eslint-plugin-react-hooks 7.1.1, eslint-plugin-react-refresh 0.5.8, globals 17.14.0, eslint-config-prettier 10.1.8.
  - **Types:** @types/react and @types/react-dom 19.3.0. React Router 8.5.1 has peers `@types/react >=19.2.0`.
  - **psycopg extra:** `python:*-slim` images ship no libpq, so depend on `psycopg[binary]` 3.3.9. SQLAlchemy 2.1.8 already maps plain `postgresql://` to psycopg 3 (`dialects/postgresql/__init__.py`: `base.dialect = dialect = psycopg.dialect`), which is consistent with the stack.
  - **Base images:** name them: `postgres:16-alpine` (already in the detail), `python:3.12-slim` (or 3.14, F9), `node:26-alpine` (F1).

### F11 — React Router 8 package name (low)

- **Location:** Stack row "React Router 8.5".
- **Evidence:** npm `react-router` latest is 8.5.1. `react-router-dom` latest is **7.18.5**, with no 8.x version published. Following older guides with `npm i react-router-dom` silently installs v7.
- **Fix:** write the row as "`react-router` 8.5" and add a convention that all routing imports come from `react-router`.

### F12 — Verification is asserted, not evidenced (low)

- **Location:** Stack header: "Verified against npm, PyPI, Docker Hub and endoflife.date on 2026-11-15."
- **Evidence:**
  - Each listed minor matches the registries (see the table below), so the numbers are real.
  - But the spine records no peer or engine checks, and those are where F1–F4 surface.
  - The memlog records only the TypeScript peer check.
  - FastAPI 0.163.1 was uploaded the same morning (2026-11-15T09:51Z), and 0.x packages (FastAPI, uvicorn, ruff, httpx) can break between minors.
- **Fix:**
  - Keep the spine's minor-level table, but state the pinning policy: Python `~=` on the exact patch in `pyproject.toml` plus a lock file; npm exact versions in `package-lock.json`.
  - Add the runtime floors (Node, Python) and the TypeScript 6 / typescript-eslint peer reason in one line each, so the next re-verification knows what to re-test.

---

## Stack rows confirmed against live sources (no action needed beyond the findings above)

| Spine row | Live value seen | Source | Fit checked |
| --- | --- | --- | --- |
| Python 3.12 | 3.12.16; EOL 2028-10-31 | endoflife.date | all backend packages require ≥ 3.10/3.11; see F9 |
| FastAPI 0.163 | 0.163.1 (2026-11-15), `requires_python >=3.11` | PyPI | `pydantic>=2.9.0`, `starlette>=0.46.0` → Starlette 1.10.0; see F3 |
| Pydantic / pydantic-settings 2.14 / 2.16 | 2.14.3 / 2.16.3 | PyPI | pydantic-settings needs `pydantic>=2.7.0`, Python ≥ 3.11 |
| SQLAlchemy 2.1 (sync, 2.0-style) | 2.1.8, `>=3.11` | PyPI + wheel | default PG driver is psycopg 3 |
| psycopg 3.3 | 3.3.9 | PyPI | SQLAlchemy extra `psycopg!=3.1.15,>=3.0.7` |
| Alembic 1.20 | 1.20.1 | PyPI | `SQLAlchemy>=2.0` |
| PyJWT / bcrypt 2.15 / 5.0 | 2.15.1 / 5.0.0 | PyPI + live run | see F6, F8 |
| uvicorn 0.54 | 0.54.0 | PyPI + wheel | default host 127.0.0.1; see F5 |
| pytest / httpx / ruff 9.2 / 0.28 / 0.17 | 9.2.0 / 0.28.1 (2024-12-06) / 0.17.5 | PyPI | httpx deprecated for TestClient; see F3 |
| PostgreSQL 16 + `btree_gist` | 16.16; EOL 2028-11-09; 18.7 newest | endoflife.date, Docker Hub | `postgres:16-alpine` updated 2026-11-12 on alpine3.24; its Dockerfile runs `make -C contrib install`, so `btree_gist` is present. (If you later move to 18, note its image moved `PGDATA` to `/var/lib/postgresql/18/docker`.) |
| React / React DOM 19.3 | 19.3.1 / 19.3.1 | npm, endoflife.date | react-dom peer `react ^19.3.1` |
| TypeScript 6.0 | 6.0.3 is last 6.x; latest is 7.0.2 | npm | claim confirmed; see F2 |
| Vite / @vitejs/plugin-react 8.3 / 6.3 | 8.3.8 / 6.3.0 | npm | plugin peer `vite ^8.0.0`; Node `^20.19.0 \|\| >=22.12.0` |
| Tailwind CSS / @tailwindcss/vite 4.3 | 4.3.5 / 4.3.5 | npm + tarball | `theme.css` opens with `@theme default {` (v4 `@theme` tokens current); plugin peer includes `vite ^8` |
| Recharts 3.10 | 3.10.1 | npm | peer react ^19 and **react-is** (F10) |
| React Router 8.5 | 8.5.1 | npm | peer react ≥ 19.2.7; Node ≥ 22.22.0 (F1); package name (F11) |
| TanStack Query 5.107 | 5.107.1 | npm | peer `react ^18 \|\| ^19` |
| Vitest / RTL / MSW 5.1 / 16.3 / 3.0 | 5.1.0 / 16.3.3 / 3.0.6 | npm | vitest peer `vite ^6.4 \|\| ^7 \|\| ^8`; DOM env missing (F4) |
| ESLint / typescript-eslint / Prettier 10.15 / 8.72 / 3.10 | 10.15.0 / 8.72.2 / 3.10.1 | npm | typescript-eslint peers `eslint ^10.0.0` and `typescript <6.1.0` |
| Docker Compose (local runtime) | Compose v2.39.1 on the project machine | `docker compose version` | Compose v2 spec; no version needed in the spine |
| GitHub Actions | checkout v7.0.1, setup-node v7.1.0, setup-python v7.1.0 | GitHub releases API | see F7 |
