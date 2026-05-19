# Output templates — review-pr

Loaded for every review (Phase 3).

## Report template — standard review

```markdown
## PR Review: {title or branch}

### Context
- **Repository**: {owner/repo}
- **Branch**: {branch} → {base}
- **Linear**: {ID — title} | "No linked Linear issue"
- **Size**: {XS/S/M/L/XL} ({N} lines in {M} files)
- **CI Status**: {passing/failing/pending} (--pr mode only)

### Commit Analysis (PER_COMMIT strategy only)
| # | Commit | Type | Summary | Observations |
|---|--------|------|---------|--------------|
| 1 | `{sha[:7]}` | {refactor/bugfix/feature/infra/test-only} | {commit message} | {key observation or "OK"} |

**Cross-commit**: {1-2 line summary, or "No cross-commit issues identified"}

### Linear Alignment
{If issue found: evaluation of adherence to acceptance criteria}
{If not found: "No linked issue — review based on code only"}

### Positives
- {Good practices, clean design, complete tests}

### Findings

#### CRITICAL ({count})
- **{file:line}** — {description} — {suggestion}

#### WARNING ({count})
- **{file:line}** — {description} — {suggestion}

#### INFO ({count})
- **{file:line}** — {description} — {suggestion}

### Stats
- Files reviewed: N
- Lines changed: +N / -N (size: XS/S/M/L/XL)
- Findings: N critical, N warning, N info

### Verdict
{APPROVE | REQUEST_CHANGES | COMMENT} — {one-line justification}
```

---

## Report template — recheck (`--recheck`)

```markdown
## Re-review: {pr_title} (PR #{pr_number})

### Context
- **Repository**: {owner/repo}
- **Branch**: {branch} → {base}
- **Prior review**: `REQUEST_CHANGES` on {review_date} (commit `{reviewed_sha[:7]}`)
- **Current head**: `{current_sha[:7]}`
- **Delta**: {N} lines in {M} files (delta size: {XS/S/M/L/XL})
- **Commits since review**: {oneline list}
- **CI Status**: {passing/failing/pending}

### Prior Findings Validation

| # | Severity | File:Line | Finding | Status |
|---|----------|-----------|---------|--------|
| 1 | {CRITICAL/WARNING/INFO} | `{file}:{line}` | {brief description} | {FIXED/NOT_FIXED/PARTIALLY_FIXED/N/A} |

**Summary**: {X}/{Y} findings resolved.

{For each NOT_FIXED or PARTIALLY_FIXED:}
#### Finding #{N} — {status}: {description}
- **File**: `{file}:{line}`
- **Original severity**: {CRITICAL/WARNING/INFO}
- **Current state**: {evidence from current code}
- **Suggestion**: {original or updated suggestion}

### New Findings in Delta

{If none:}
No new issues found in changes since `{reviewed_sha[:7]}`.

{If any — same format as standard review:}
#### CRITICAL ({count})
#### WARNING ({count})
#### INFO ({count})

### Linear Alignment
{Carried forward from prior review, or updated if delta changes the assessment}

### Stats
- Prior findings: {N} total ({X} resolved, {Y} pending)
- New findings in delta: {N} ({M} lines in {K} files)

### Verdict
{APPROVE | REQUEST_CHANGES | COMMENT} — {justification}
```

**Recheck verdict logic:**

| Condition | Verdict |
|---|---|
| Any prior CRITICAL NOT_FIXED, or new CRITICAL in delta | `REQUEST_CHANGES` |
| All resolved, no new CRITICALs | `APPROVE` |
| All resolved, but new WARNINGs in delta | `COMMENT` |
| Prior WARNING/INFO pending, no CRITICALs | `COMMENT` |
