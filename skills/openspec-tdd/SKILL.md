---
name: openspec-tdd
description: SDD+TDD+CodeReview pipeline. Use commands: /openspec-tdd:start, :continue, etc.
version: 2.0.0
---

# OpenSpec-TDD Resources

This skill provides reference files and command pointers. Do not execute directly.

## Commands

- `/openspec-tdd:start "description"` – full pipeline
- `/openspec-tdd:continue` – resume
- `/openspec-tdd:implement <task-id>` – TDD a single task
- `/openspec-tdd:review-code` – code review
- `/openspec-tdd:review-arch` – architecture review
- `/openspec-tdd:verify` – validate specs & tests
- `/openspec-tdd:finish` – final archive
- `/openspec-tdd:status` – show progress
- `/openspec-tdd:reset [soft|hard]` – reset state
- `/openspec-tdd:wizard` – interactive guide
- `/openspec-tdd:help` – list all commands

## Reference Files

- Code review checklist: see `../code-review/review-checklist.md`
- TDD guidelines: `references/tdd-guidelines.md`
- Quality gates: `references/quality-gates.md`

## Configuration (optional)

Create `.openspec/config.json`:

```json
{
  "tdd": {
    "testCommand": "npm run test:ci",
    "reviewChecklistPath": "./custom-checklist.md",
    "taskTimeMinutes": 8
  }
}
```
