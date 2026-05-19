# Large PR prioritization — review-pr

Loaded only for L (500–1000 lines) and XL (1000+ lines) PRs.

## Risk-based file prioritization

| Tier | Criteria | Scrutiny level |
|---|---|---|
| 1 — Critical | New source files, migrations, auth, financial/business logic | Deep review: read whole file, verify invariants |
| 2 — Standard | Modified source files | Full diff review |
| 3 — Scan | Test files | Verify coverage and anti-patterns, not detailed logic |
| 4 — Skip | Binaries, auto-generated, formatting-only | Note presence in report, don't review content |
