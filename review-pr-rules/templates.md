# Output template — review-pr

Loaded by the CI workflow. The local skill's `--recheck` mode uses a separate template (see local skill docs).

## Standard review template

Format the published review exactly as:

```markdown
## PR Review: <title or branch>

### Context
- **Repository**: <owner/repo>
- **Branch**: <branch> → <base>
- **Linear**: <ID — title>  OR  "No linked Linear issue"
- **Size**: <XS/S/M/L/XL> (<N> lines in <M> files)
- **CI Status**: <passing/failing/pending>

### Commit Analysis (PER_COMMIT only)
| # | Commit | Type | Summary | Observations |
|---|--------|------|---------|--------------|
| 1 | `<sha[:7]>` | <refactor/bugfix/feature/infra/test-only> | <message> | <observation or "OK"> |

**Cross-commit**: <1-2 line summary, or "No cross-commit issues identified">

### Linear Alignment
<If issue found: evaluation of adherence to acceptance criteria>
<If not: "No linked issue — review based on code only">

### Positives
- <Good practices, clean design, complete tests>

### Findings

#### CRITICAL (<count>)
- **<file:line>** — <description> — <suggestion>

#### WARNING (<count>)
- **<file:line>** — <description> — <suggestion>

#### INFO (<count>)
- **<file:line>** — <description> — <suggestion>

### Stats
- Files reviewed: N
- Lines changed: +N / -N (size: XS/S/M/L/XL)
- Findings: N critical, N warning, N info

### Verdict
<APPROVE | REQUEST_CHANGES | COMMENT> — <one-line justification>
```

Use `<...>` placeholders as guides; fill in real values. Omit empty sections with "None" so the reviewer sees they were checked.
