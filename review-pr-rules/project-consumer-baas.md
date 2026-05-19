# Project — `consumer-baas`

## Phase 2 checklist

- Clear separation between webhook receiver (`lambda_webhook_receiver.py`) and queue consumer (`lambda_queue_consumer.py`) — never mix responsibilities
- Webhooks must be idempotent (same payload twice = same effect); verify idempotency keys/guards before side effects
- SQS messages follow `docs/05-message-specification.md`; webhook payloads follow `docs/10-webhook-specification.md`
- Asaas API calls isolated in `asaas_client.py`; don't call API directly from handlers/controllers
- Secrets via AWS Secrets Manager (`app/core/secrets.py`), never hardcoded or via env var in prod
- SQS batch failures must return `batchItemFailures` for selective retry (don't fail the whole batch)
- Tests with Localstack (not direct AWS SDK mocks)
