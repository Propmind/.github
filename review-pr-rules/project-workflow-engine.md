# Project — `workflow-engine` (propmind-core/workflow-engine)

## Phase 2 checklist

- Determinism required: no `datetime.now()`, `random`, or direct API/DB calls inside workflows
- Use `workflow.now()` and `workflow.random()` inside workflows
- External operations are activities, never workflows
- Task queue format: `{env}-{workflow_type}` (e.g. `dev-validation`)
- Workflow ID format: `{workflow_type}:{business_entity_id}[:{cycle}]`
- Required search attributes: `environment`, `tenant_id`, `workflow_type`, `business_status`
- Distinguish technical errors from business errors
