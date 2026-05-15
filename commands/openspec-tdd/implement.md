---
description: Run TDD on a specific task (skip earlier phases)
arguments:
  - name: task_id
    required: true
---

Direct TDD on a specific task without running earlier phases.

## 1. Identify current change

Read `.scratch/openspec-tdd/current-change`. If missing, scan `openspec/changes/` (non-archive) and ask user to select.

Also read `.scratch/openspec-tdd/branch` and compare with `git branch --show-current`. If mismatch, warn.

## 2. Locate task

Read `openspec/changes/<change-name>/tasks.md`. Find the line matching `task_id`.

Supports:

- Numeric index: `3` → third unchecked task
- Sub-index: `3.2` → second sub-task of task 3
- Line number: `L45` → line 45 in tasks.md

If task is already `[x]`, output: "Task already completed." and abort.

## 3. Run TDD

Call `/tdd` with the task description from `tasks.md`.

## 4. Mark complete

After TDD cycle succeeds (tests green), mark the task as `[x]` in `tasks.md`.

## 5. Output

```
✅ Task <task_id> complete

   Remaining: <N> tasks

   Run `/openspec-tdd:continue` to proceed with the next task,
   or `/openspec-tdd:implement <id>` to target another specific task.
```

## 6. Error handling

- If no change is active: "No active change. Run `/openspec-tdd:start` first."
- If `tasks.md` not found: "tasks.md not found. Run `/openspec-tdd:start` to generate it."
- If task_id not found: "Task '<task_id>' not found in tasks.md. Check `/openspec-tdd:status` for available tasks."
- If `/tdd` fails: "TDD cycle failed. Fix the failing test and re-run `/openspec-tdd:implement <task_id>`."
