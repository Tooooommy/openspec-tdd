---
description: Final verify and archive (with extra checks)
---

Run final validation, tests, and archive the completed change.

## 1. Pre-archive checks

### Check tasks completion

Read `tasks.md` for the current change. Ensure all tasks are `[x]`.

If any `[ ]` remain:

```
❌ Not all tasks are complete.

   Remaining: 3 tasks

   Run `/openspec-tdd:continue` to finish TDD implementation first.
```

Abort.

### Check code review

If `.scratch/openspec-tdd/review-code-passed` is missing:

```
⚠️  Code review has not passed.

   Run `/openspec-tdd:review-code` first.
```

Ask: "Skip code review and proceed anyway? (y/n)"

If no → abort.

### Check architecture review

If `.scratch/openspec-tdd/review-arch-passed` is missing:

```
⚠️  Architecture review has not passed.

   Run `/openspec-tdd:review-arch` first.
```

Ask: "Skip architecture review and proceed anyway? (y/n)"

If no → abort.

## 2. Final validation

Run: `openspec validate <change-name> --strict`

If exit code != 0:

- Output errors
- Exit with: "Spec validation failed. Fix issues and re-run `/openspec-tdd:finish`."

## 3. Run test suite

Auto-detect test command (same logic as `verify.md`):

1. Custom from `.openspec/config.json`
2. `npm test`
3. `pytest`
4. `go test ./...`
5. `cargo test`

If exit code != 0:

- Output failing tests
- Exit with: "Tests failed. Fix failing tests and re-run `/openspec-tdd:finish`."

## 4. Archive

Run: `openspec archive <change-name> --yes`

If exit code != 0:

- Output error
- Exit with: "Archive failed. Check the error above."

## 5. Cleanup

Remove state files:

- `.scratch/openspec-tdd/current-change`
- `.scratch/openspec-tdd/branch`
- All phase flags: `requirements-confirmed`, `specs-validated`, `tasks-refined`, `tdd-complete`, `review-code-passed`, `review-arch-passed`

(Keep the `.scratch/openspec-tdd/` directory itself for future runs.)

## 6. Output summary

```
🎉 Archive Complete

   Change:     <change-name>
   Archived:   changes/archive/<change-name>/
   Specs:      Updated in specs/

   ✅ Spec validation: PASSED
   ✅ Test suite:      PASSED
   ✅ Archive:         SUCCESS

   Pipeline finished. Start a new feature with `/openspec-tdd:start`.
```

## 7. Error handling

- If no change is active: "No active change. Run `/openspec-tdd:start` first."
- If `tasks.md` is missing: "tasks.md not found. The change may be corrupted. Run `/openspec-tdd:reset hard` and start over."
- If archive fails due to merge conflicts: "Archive failed due to conflicts. Resolve conflicts in specs/ and re-run."
