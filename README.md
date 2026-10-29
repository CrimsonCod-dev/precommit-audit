# precommit-audit

[![skills.sh](https://skills.sh/b/CrimsonCod-dev/precommit-audit)](https://skills.sh/CrimsonCod-dev/precommit-audit)

An [Agent Skill](https://agentskills.io) that audits your uncommitted changes before you commit them: an independent subagent reviews the diff, every finding is checked against the real code, only confirmed fixes are applied, and the run ends with an honest `COMMIT-READY: YES` or `COMMIT-READY: NO` verdict.

The skill never commits. The verdict is the deliverable; you decide what happens next.

## Why

A reviewer that shares your context shares your blind spots. This skill dispatches a fresh subagent that sees only the repository — then verifies every one of its findings itself, because subagents confidently misquote code. Confirmed issues get fixed, hallucinated ones get dropped, and failing project checks force a NO.

## Install

Requires Node.js 22+.

```bash
npx skills add CrimsonCod-dev/precommit-audit
```

Installs into the current project. For every project on the machine:

```bash
npx skills add CrimsonCod-dev/precommit-audit -g
```

### Manual install

Copy `skills/precommit-audit/` into your agent's skills directory:

| Agent | Directory |
|-------|-----------|
| Claude Code | `~/.claude/skills/` |
| OpenCode | `~/.config/opencode/skills/` |
| Shared agent skills | `~/.agents/skills/` |

## Usage

Ask in plain language, in any project with uncommitted changes:

- "pre-commit audit"
- "check my uncommitted changes"
- "review my diff"
- "ready to commit?"
- «проверь перед коммитом»

## What it does

| Step | Action |
|------|--------|
| 1 | Collects `git status` and the full diff, including untracked files |
| 2 | Dispatches one read-only subagent for an independent review |
| 3 | Opens every finding at `file:line` and marks it CONFIRMED, REJECTED, or UNVERIFIED |
| 4 | Fixes confirmed findings and runs the project's own tests and linters |
| 5 | Reports scope, findings, changes, checks, and the verdict |

The report ends with exactly one line:

```
COMMIT-READY: YES
```

or

```
COMMIT-READY: NO — <reasons>
```

## Update

```bash
npx skills check
npx skills update precommit-audit
```

Add `-g` to update the global install. Manual installs: replace the copied folder with the latest `skills/precommit-audit/`.

## Uninstall

```bash
npx skills remove precommit-audit
```

## Requirements

- `git`
- An agent that supports Agent Skills and can dispatch a read-only subagent — tested with Claude Code and OpenCode

## How it works

The full instructions live in [`skills/precommit-audit/SKILL.md`](skills/precommit-audit/SKILL.md). The short version:

1. **Evidence only.** Every finding, fix, and verdict reason is backed by `file:line` checked in the real code.
2. **Fresh eyes, untrusted output.** The subagent has no conversation bias, but its report is treated as a lead, not a fact.
3. **Minimal fixes.** No drive-by refactoring; failing checks are findings and force a NO until fixed.
4. **No commit.** Staging, committing, and pushing stay with you.

## Contributing

Issues and pull requests are welcome. Edit `skills/precommit-audit/SKILL.md`, then validate:

```bash
npx -y skills-ref@0.1.5 validate ./skills/precommit-audit
```

CI runs the same check on every push and pull request.

## License

[MIT](LICENSE)
