# Common rules — review-pr

Loaded for every review.

## Common checklist (all projects)

- Commits follow Conventional Commits `type(scope): description`?
- Any secrets, API keys, or tokens in the diff?
- Changes outside the PR/task scope?
- If Linear issue linked: do changes satisfy the acceptance criteria?
- If author contested any prior findings: verify against actual code before emitting or discarding.

## Reviewer rules

- **Flag, don't fix** — report issues and directions; never rewrite the author's code.
- **Cite evidence** — every finding must reference `file:line`. Vague findings are not findings.
- **Proportionality** — don't nitpick style when there are logic bugs.
- **Acknowledge quality** — good patterns, clean abstractions, and complete tests deserve recognition.
- **Zero false positives** — verify each finding against real code; when in doubt, lower severity or note uncertainty.
- **Author comments are evidence** — if the author contested a finding with code references, verify before carrying forward.

## Python — all services

- PEP 8: snake_case, import organization.
- Type hints on new/modified function signatures.
- **Blocking/CPU calls in async contexts.** A sync blocking call inside `async def` stalls every concurrent request on the loop. Grep new code for: `WeasyPrint.write_pdf`, `requests.`, sync `httpx.Client`, `time.sleep`, sync `boto3`, `PIL`/`Pillow`, large payload hashing, heavy `json.dumps`/`loads`, blocking DB drivers. Fix: `await asyncio.to_thread(fn, *args)` or make the function async. Flag any match as WARNING.
- **Third-party callback return-type contract.** When a PR adds a callback passed to a library (`WeasyPrint url_fetcher`, Pydantic validators, FastAPI `Depends`, SQLAlchemy event listeners), the type annotation can lie — it matches the docstring, not reality. Open the library source to confirm the expected return type. Real example: `_block_remote_fetcher` annotated `-> dict` returned `HTTPResponse`, raising `TypeError` at runtime.
- Tests for new/modified functions.

## Cross-commit analysis (PER_COMMIT only)

Apply after per-file review:

- **DRY cross-commit** — same pattern/helper/constant duplicated across commits.
- **Approach inconsistency** — same problem solved differently across commits.
- **Scope drift** — commits stray from PR theme (INFO; suggest split).
- **Evolution regression** — commit N fixes something commit N-1 introduced wrong (context, not a finding by itself).
- **PR body vs reality** — described changes absent, or present but undescribed (INFO).

## Failure mode analysis (Tier 1 files only)

For migrations, auth, financial logic, ask:

- "Fails silently?" — insufficient error handling, fire-and-forget without tracking.
- "Pre-existing data?" — backward compat breaks, orphans, queries missing old states.
- "All envs (dev/staging/prod)?" — hardcoded config, fixed schemas, missing flags.
- "Runs twice?" — missing idempotency, duplicates, non-idempotent side effects.

Only emit a finding when the answer reveals a **concrete problem** — no speculative findings.
