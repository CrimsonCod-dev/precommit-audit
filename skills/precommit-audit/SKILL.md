---
name: precommit-audit
description: Pre-commit audit of uncommitted changes — an independent subagent reviews the diff, every finding is verified against the real code, only confirmed fixes are applied, and the run ends with an honest COMMIT-READY YES/NO verdict. Use whenever the user wants changes checked before committing — "pre-commit audit", "check my uncommitted changes", "review my diff", "ready to commit?", «проверь перед коммитом», «аудит uncommitted», «готов ли код к коммиту» — even when the word "audit" is not used.
license: MIT
---

# Pre-Commit Audit

Pipeline: collect diff → independent subagent review → verify findings → fix only what is confirmed → honest verdict. Do not commit: the verdict is the deliverable; the user decides.

Rule for the whole run: **evidence only**. Every claim — finding, fix, verdict reason — is backed by `file:line` you checked yourself in the real code. No "seems like", no invented test results.

## 1. Collect the diff

- `git status --porcelain` plus `git diff HEAD` (covers staged and unstaged tracked files).
- No commits yet? `git diff HEAD` fails with `ambiguous argument 'HEAD'` — use `git diff --cached` plus `git diff` instead.
- Gotcha: untracked files do not appear in `git diff HEAD` — read them in full, or the audit is incomplete.
- Nothing to audit → say so and stop.

## 2. Independent subagent review

Dispatch one general-purpose subagent with your agent's subagent/task tool (Claude Code: Agent/Task; OpenCode: `subagent`), explicitly read-only. The prompt gives it:

- Role: independent expert reviewer with fresh eyes; nothing from the main conversation is input.
- Task: run the diff commands from step 1 itself, read untracked files, skim README/CLAUDE.md/SPEC for project conventions.
- Review targets: logic errors and regressions; security (secrets, injection, path traversal, unsafe defaults); incomplete work (stubs, TODOs, doc drift); tests covering changed behavior; project style.
- Output per finding: severity (critical | major | minor), `file:line`, code quote, why it is a problem. "No findings" is a valid result.

Why fresh eyes: the subagent has no stake in the changes and no conversation bias — but its report is still untrusted input (step 3).

## 3. Verify every finding yourself

Subagents fabricate and misquote. For each finding, open `file:line` in the real code and classify:

- **CONFIRMED** — reproducible in the code → goes to fixes.
- **REJECTED** — not reproducible or the quote does not match → drop it and state why in the report.
- **UNVERIFIED** — cannot decide → do not fix, do not claim; list as an open question. If it could break production, it forces NO.

Never accept a finding on the subagent's quote alone.

## 4. Fix confirmed findings, run the checks

- Fixes are minimal, in the surrounding code style, with no drive-by refactoring.
- Then run the project's own checks — discover the real commands from README/CLAUDE.md/Makefile (tests, linters). If the project has none, say so plainly; never invent results. Failing checks count as confirmed findings: fix them, or they force NO.
- No confirmed findings → change nothing and write: "No confirmed findings; no changes made."

## 5. Verdict

Calibration: the goal is risk reduction, not perfect code — style nits do not force NO; broken logic, secrets in the diff, failing checks, or incomplete work do.

The last line of the reply is exactly one of:

- `COMMIT-READY: YES`
- `COMMIT-READY: NO — <reasons>`

Write the rest of the report in the user's language.

## Report format

1. **Scope** — files reviewed, diff size.
2. **Findings** — table: finding → CONFIRMED / REJECTED / UNVERIFIED (+ why) → action taken.
3. **Changes** — fixes applied, or "no changes made".
4. **Checks** — real commands run and their results.
5. **Verdict** — the `COMMIT-READY:` line.
