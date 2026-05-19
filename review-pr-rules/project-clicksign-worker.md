# Project — `clicksign-worker`

## Phase 2 checklist

- Clear separation: webhook handler API Gateway (`lambda_webhook_handler.py`) enqueues and responds fast; SQS worker (`lambda_function.py`) processes
- Clicksign API calls isolated in `clicksign_client.py`
- Webhook handler validates signature via `CLICKSIGN_WEBHOOK_KEY` where applicable
- SQS worker returns `batchItemFailures` for selective retry; don't fail the whole batch
- Config exclusively via env vars in `app/config/settings.py`; never hardcoded
- Terraform infra in `infra/live/dev/` must accompany changes that require new AWS resources
- DTOs/enums/exceptions organized in `app/dto/`, `app/enum/`, `app/exception/`
