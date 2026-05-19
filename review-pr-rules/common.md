# Common reference — review-pr

Loaded for every review regardless of project.

## Project profiles (Phase 0)

| Repo / subproject | Type | Convention docs to read | Project fragment |
|---|---|---|---|
| `propmind-core` (root) | uv-workspace | `CLAUDE.md`, `RELEASE.md`, `MIGRATION.md` | — |
| `propmind-core/api` | python-fastapi | `AGENTS.md` | `project-api.md` |
| `propmind-core/workflow-engine` | python-temporal | `AGENTS.md`, `README.md` | `project-workflow-engine.md` |
| `propmind-core/shared` | python-shared-contracts | `README.md` | — |
| `control-panel-ui` | react-typescript | `AGENTS.md`, `FRONTEND-PATTERNS.md`, `DRAWER-PATTERNS.md`, `LAYOUT-STABILITY.md`, `CARD-PATTERNS.md` | `project-control-panel-ui.md` |
| `boleto-extractor` | python-ai-agent | `CLAUDE.md` | `project-boleto-extractor.md` |
| `consumer-baas` | python-lambda | `CLAUDE.md`, `README.md`, `docs/02-architecture.md`, `docs/05-message-specification.md`, `docs/09-testing.md`, `docs/10-webhook-specification.md` | `project-consumer-baas.md` |
| `clicksign-worker` | python-lambda | `README.md` | `project-clicksign-worker.md` |

If the project is not in this table, apply the common checklist only and note: "Unmapped project — review based on general conventions."

---

## Batch size classification

| Size | Lines | Action |
|---|---|---|
| XS | < 50 | No note |
| S | 50–200 | No note |
| M | 200–500 | Healthy range |
| L | 500–1000 | Emit WARNING suggesting where to split |
| XL | 1000+ | Make batch size the **main finding** — defect detection drops to ~28%; focus on how to decompose |

---

## --recheck preconditions (Phase 0)

1. Fetch `get_pull_request_reviews` + `get_pull_request` in parallel
2. Filter reviews where `state == "CHANGES_REQUESTED"`; if none found, stop
3. Select most recent `REQUEST_CHANGES` review; extract `reviewed_sha`, `review_body`, `review_date`
4. Guard clauses (check in order):
   - `reviewed_sha == current_sha` → "No new commits since last review" → STOP
   - `review_body` doesn't contain `### Findings` → "Prior review not in standard format; use `--pr NUMBER`" → STOP
   - Force-push detected via `git fetch origin && git merge-base {reviewed_sha} {current_sha}` → STOP

---

## Common checklist (Phase 2 — all projects)

- Commits follow Conventional Commits `type(scope): description`?
- Any secrets, API keys, or tokens in the diff?
- Changes outside the PR/task scope?
- If Linear issue linked: do changes satisfy the acceptance criteria?
- If author contested any prior findings: verify against actual code before emitting or discarding.

---

## Reviewer rules (apply throughout)

- **Flag, don't fix** — report issues and suggest directions; never rewrite the author's code
- **Cite evidence** — every finding must reference `file:line`; vague findings are not findings
- **Proportionality** — don't nitpick style when there are logic bugs
- **Acknowledge quality** — good patterns, clean abstractions, and complete tests deserve recognition
- **Zero false positives** — verify each finding against real code; when in doubt, lower severity or note uncertainty
- **Author comments are evidence** — if the author contested a finding with code references, verify before carrying it forward

---

## Python — all services (apply to all Python projects: `api`, `workflow-engine`, `boleto-extractor`, `consumer-baas`, `clicksign-worker`)

- PEP 8: snake_case, import organization
- Type hints on new/modified function signatures
- **Blocking/CPU calls in async contexts:** an async function runs on a single event loop — a sync blocking call stalls every concurrent request. Grep new code under `async def` for: `WeasyPrint.write_pdf`, `requests.`, `httpx.Client` (sync), `time.sleep`, sync `boto3`, `PIL`/`Pillow`, hashing of large payloads, heavy `json.dumps`/`json.loads`, blocking DB drivers. Fix pattern: `await asyncio.to_thread(func, *args)` or make the function async. Flag any match as WARNING.
- **Third-party callback return-type contract:** when a PR introduces a custom callback passed to a library (WeasyPrint `url_fetcher`, Pydantic validators, FastAPI `Depends`, SQLAlchemy event listeners), the type annotation can lie — it matches the docstring, not reality. Open the library's docs or source to confirm the expected return type/shape, don't trust the annotation alone. Real example: `_block_remote_fetcher` annotated `-> dict` returned `urllib.request.urlopen(url)` (an `HTTPResponse`), causing `TypeError` at runtime on any data URI.
- Tests for new or modified functions

---

## Cross-commit analysis (PER_COMMIT strategy)

Apply after per-file review:

- **DRY cross-commit**: same pattern, dict, helper, or constant introduced in multiple commits
- **Approach inconsistency**: same problem solved differently across commits
- **Scope drift**: commits that stray from the PR's main theme (INFO — suggest separate PR)
- **Evolution regression**: commit N fixes something commit N-1 introduced incorrectly (context for scrutiny, not a finding itself)
- **PR body vs reality**: changes described but absent, or present but not described (INFO)

---

## Failure mode analysis (Tier 1 files only)

For each Tier 1 change, ask:

| Question | What it detects |
|---|---|
| "What if this fails silently?" | Insufficient error handling, fire-and-forget without tracking |
| "What happens to pre-existing data?" | Backward compat breaks, orphaned data, queries missing old states |
| "Does this work in all envs (dev/staging/prod)?" | Hardcoded config, fixed schemas, missing feature flags |
| "What if this runs twice?" | Missing idempotency, duplicates, non-idempotent side effects |

Only generate a finding when the answer reveals a **concrete problem** in the code — no speculative findings.
