# Project — `propmind-ai-agents`

Loaded for reviews in `Propmind/propmind-ai-agents`. This repository's reviews are **comment-only**: the verdict never blocks a merge, and the agent that authored the PR is required by the repo's `CLAUDE.md` to answer every finding.

## Scope: only what static analysis cannot see

Ruff, mypy, complexipy, import-linter and jscpd already run on every PR and cover form. Repeating their work is the fastest way for this review to become ignored noise.

1. **Adherence to what was asked.** The diff does what the PR title and description promise, nothing less and nothing more. Unrequested "bonus" code, a refactor smuggled into a fix, and scope that grew along the way are valid findings.
2. **Silent failure.** `except: pass`, an `except Exception` that swallows and continues, a default return that masks an error, fire-and-forget with no observability. This repo has a costly precedent: a proactive WhatsApp notice was rejected by Meta for three days and was only noticed because someone read a log for another reason. An error path that leaves no trace is a finding.
3. **Tests that assert nothing.** A test that runs the code without checking the result, a test that only mirrors the implementation (change the code, change the test, it never fails), or a mock so broad the test would pass with an empty function. Ask of every new test: *what bug would this catch?*
4. **Prompt change without an eval.** Any change to `agent/onboarding/system_prompts.py`, `agent/broker/*`, or `documents/schemas.py` (extraction instructions) must ship with a matching eval. Editing a tuned prompt has measurably regressed this repo before. A prompt change without an eval is a finding, even when it looks obvious.
5. **External I/O errors.** Calls to Meta, propmind-core, S3, Gemini and Postgres must answer explicitly: is a timeout set? is there a retry? what happens to the user when it fails? A missing timeout is a finding.

## Do not comment on

- Style, naming, formatting, import order, line length.
- Complexity, function or file size.
- Import boundaries and layers.
- Known vulnerabilities in dependencies.

Each of these has a dedicated gate. If you saw something there, the gate is misconfigured: the finding is about the gate, not the code.

## Severity mapping

Use the standard contract: data loss, silent failure in production or a leaked secret → **CRITICAL**; a likely bug or an illusory test → **WARNING**. Fragility and observations are not findings. At most 10 findings: if there are more, report the 10 most severe and one line saying how many were left out.
