# Project — `propmind-nfse-emitter`

NFS-e emitter (Sefin Nacional) on AWS Lambda. Consumes ready-made commands from
the core over SQS, signs, transmits over mTLS, reports back. It decides nothing:
the domain lives in `propmind-core`.

## Phase 2 checklist

- **Bytes on the wire are sacred.** A change to the DPS template, the event template, the XMLDSIG profile or the gzip+base64 transport requires regenerating the corpus (`test/fixtures/dps-corpus.json`, produced by running the LEGACY builder) and proving byte-for-byte equality. Code around them may improve freely as long as the corpus still passes
- **The input contract is the golden fixture mirrored from propmind-core** (`test/fixtures/nfse_emission_command.golden.json`, sha256 pinned in `test/golden-fixture.spec.ts`). Changing it means changing fixture + hash in BOTH repos in the same movement — a PR that touches one side only is incomplete
- **No route signs.** Signing happens only while consuming a command from the queue; an HTTP signing oracle must not come back
- **No local state.** State belongs to the core, XML lives in S3, and the A1 certificate lives only in Secrets Manager — never in Terraform state, never in an env var
- **The Sefin response taxonomy drives the SQS retry**: `authorized` / `already_registered` (real-world idempotency) / `rejected` (terminal) / `transport` (retryable). Misclassifying turns an issued note into `Error`, or burns a delivery attempt — check the class before the message text
- **A fixture describing an external service's response must be CAPTURED, not inferred from documentation.** An invented fixture repeats whatever assumption the code makes and passes. Real case: the Sefin serializes error FIELDS in PascalCase (`Codigo`/`Descricao`) while the docs say camelCase, so every error code silently fell back to the HTTP status. Watch for silent-fallback parsing (`?? httpStatus`, `|| raw`), which turns a missing field into a plausible value
- **`Buffer.from(...).buffer` is the shared pool, not the view.** Slicing a key or certificate without explicit `byteOffset`/`byteLength` bounds feeds prefix garbage to `importKey`, non-deterministically (passes locally, fails in CI)
- **Trim credentials before comparing or sending.** A secret written with `echo` keeps a trailing newline, and an HTTP header cannot carry one — the padded and trimmed copies then disagree and a correct key answers 401
- **`NFSE_AMBIENTE` is a STACK decision, never a per-command one.** `prod_restrita` vs `producao`; flipping to production is an act of the cutover, not a code change
- Comments, docstrings and test names in **English**; domain content stays in the original — patterns matched against the Sefin's own prose, and sentences quoted from it as evidence, must not be translated
- Money arrives as a validated two-decimal string and the emitter performs **no arithmetic** — the core is the authority (the legacy builder recomputed ISS in float and that was a defect)
