---
description: List all available OpenSpec-TDD commands
---

# OpenSpec-TDD Help

## Pipeline Commands

| Command                         | Description                                  |
| ------------------------------- | -------------------------------------------- |
| `/openspec-tdd:start "desc"`    | Start full 7-phase pipeline                  |
| `/openspec-tdd:wizard`          | Interactive step-by-step guide               |
| `/openspec-tdd:continue`        | Resume pipeline from last interruption       |
| `/openspec-tdd:implement <id>`  | Run TDD on a single task directly            |
| `/openspec-tdd:finish`          | Final validation, tests, and archive         |

## Review Commands

| Command                      | Description                    |
| ---------------------------- | ------------------------------ |
| `/openspec-tdd:review-code`  | Run code review (checklist)    |
| `/openspec-tdd:review-arch`  | Run architecture review        |

## Utility Commands

| Command                      | Description                          |
| ---------------------------- | ------------------------------------ |
| `/openspec-tdd:status`       | Show pipeline progress and branch    |
| `/openspec-tdd:verify`       | Validate specs and run tests         |
| `/openspec-tdd:reset [soft|hard]` | Reset pipeline state            |
| `/openspec-tdd:help`         | Show this help                       |

## Quick Reference

```
Start:    /openspec-tdd:start "Add user login"
Resume:   /openspec-tdd:continue
Status:   /openspec-tdd:status
Reset:    /openspec-tdd:reset soft
```

## File Locations

```
commands/openspec-tdd/   ← Command implementations
skills/openspec-tdd/     ← Pipeline skill + references
skills/code-review/      ← Standalone code review skill
.openspec/config.json    ← Optional configuration
.scratch/openspec-tdd/   ← Pipeline state (auto-managed)
```

## Troubleshooting

See `references/troubleshooting.md` for common issues.