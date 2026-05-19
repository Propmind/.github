# Project — `api` (propmind-core/api)

## Phase 2 checklist

- Route → Resource → Validator pattern respected
- `get_session_context()` does **not** auto-commit — resource functions that mutate ORM state must call `await session.commit()` explicitly before returning (and `await session.refresh(obj)` when the return value reads from ORM attributes). Missing commit → 200 OK with identity map masking the bug → silent write loss. See `propmind-core/api/AGENTS.md` "Session commits are explicit"
- Validators called before any data mutation
- Pydantic schemas for request/response
- Security dependencies on routes (`RequireAuth`, `RequireAdmin`, `RequireAdminOrOperation`, `RequireAgency`)
- Tests in `tests/unit/`, `tests/integration/`, `tests/e2e/` with pytest markers
- Private validator methods (e.g. `_validate_x`) must be called from the public method (`validate()`); grep to confirm call site — method without call site is dead code
- Double-write: if a factory/service call already persists a field on the returned object, don't set it again; one write only
- Validator tests must call the **public method** (`validate()`), not private methods directly — testing a private method in isolation doesn't prove wiring
