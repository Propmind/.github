# Project — `workflow-engine` (propmind-core/workflow-engine)

Semgrep covers: `datetime.now()`, `datetime.utcnow()`, `time.time()`, `random.*` inside workflow definitions · `start_workflow()` without `search_attributes=`. Don't re-emit these.

Remaining checklist:

- External operations are activities, never workflows.
- Task queue format: `<env>-<workflow_type>` (e.g. `dev-validation`).
- Workflow ID format: `<workflow_type>:<business_entity_id>[:<cycle>]`.
- Required search attributes: `environment`, `tenant_id`, `workflow_type`, `business_status` (semgrep flags missing kwarg; check VALUES are sensible).
- Distinguish technical errors from business errors.
