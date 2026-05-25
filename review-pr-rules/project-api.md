# Project — `api` (propmind-core/api)

- Route → Resource → Validator pattern respected.
- `get_session_context()` does NOT auto-commit. Resource functions that mutate ORM state must call `await session.commit()` before returning (and `await session.refresh(obj)` if reading ORM attrs after). Missing commit = silent write loss. See `api/AGENTS.md` "Session commits are explicit". (Semgrep already catches `session.add/delete` without commit; flag other write paths like `session.execute(update(...))`.)
- Validators called before any data mutation.
- Pydantic schemas for request/response.
- Security deps on routes: `RequireAuth`, `RequireAdmin`, `RequireAdminOrOperation`, `RequireAgency`.
- Tests in `tests/unit/`, `tests/integration/`, `tests/e2e/` with pytest markers (semgrep checks marker presence).
- Private validator methods (`_validate_x`) must be called from public `validate()`. Grep call site; if absent, it's dead code.
- Double-write: don't set a field both inside a factory/service call AND on the returned object — pick one.
- Validator tests call the **public** `validate()`, not private methods directly.
