---
description: Show pipeline status with branch info
---

Display the current state of the OpenSpec-TDD pipeline.

## 1. Identify current change

Read `.scratch/openspec-tdd/current-change`. If missing, scan `openspec/changes/` (non-archive).

## 2. Check branch

Read `.scratch/openspec-tdd/branch` and compare with `git branch --show-current`.

If mismatch:

```
⚠️  Branch mismatch!
   Recorded: <recorded-branch>
   Current:  <current-branch>

   Run `/openspec-tdd:reset soft` to clear state, then switch branches.
```

## 3. Check phase flags

Read `.scratch/openspec-tdd/` and determine current phase:

| Flag File                | Phase           | Status  |
| ------------------------ | --------------- | ------- |
| `requirements-confirmed` | 1. Requirements | ✅ / ❌ |
| `specs-validated`        | 2. Specs        | ✅ / ❌ |
| `tasks-refined`          | 3. Tasks        | ✅ / ❌ |
| `tdd-complete`           | 4. TDD          | ✅ / ❌ |
| `review-code-passed`     | 5. Code Review  | ✅ / ❌ |
| `review-arch-passed`     | 6. Architecture | ✅ / ❌ |

## 4. Task progress

If `tasks.md` exists, count:

- Total tasks
- Completed (`[x]`)
- Remaining (`[ ]`)

Show: `Tasks: 7/12 completed (58%)`

## 5. Output template

```
📊 OpenSpec-TDD Status

Change:    <change-name>
Branch:    <branch> (matched ✅ / mismatched ⚠️)

Phase Progress:
  1. Requirements:      ✅ Confirmed
  2. Specs:             ✅ Validated
  3. Tasks:             ✅ Refined
  4. TDD:               🔄 In Progress (7/12 tasks)
  5. Code Review:       ❌ Not started
  6. Architecture:      ❌ Not started
  7. Archive:           ❌ Not started

Next: Run `/openspec-tdd:continue` to resume TDD implementation.
```

## 6. Edge cases

- If no change is active: "No active pipeline. Start one with `/openspec-tdd:start \"description\"` or `/openspec-tdd:wizard`."
- If all phases complete but not archived: "All phases complete. Run `/openspec-tdd:finish` to archive."
- If `tasks.md` is missing but `specs-validated` exists: warn that tasks file is missing.
