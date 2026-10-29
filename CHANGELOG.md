# Changelog

All notable changes to this project are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-10-29

### Added

- Initial public release of the `precommit-audit` skill.
- Independent subagent review of uncommitted changes.
- Verification of every finding against the real code (CONFIRMED / REJECTED / UNVERIFIED).
- Fixes limited to confirmed findings, followed by the project's own checks.
- `COMMIT-READY: YES` / `COMMIT-READY: NO` verdict.
- Fallback for repositories without commits.
- Agent-agnostic subagent dispatch instructions (Claude Code, OpenCode).
