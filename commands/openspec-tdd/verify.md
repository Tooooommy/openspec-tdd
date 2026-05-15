---
description: Validate specs and run tests (no archive)
---

Run specification validation and test suite without archiving.

## 1. Identify current change

Read `.scratch/openspec-tdd/current-change`. If missing, scan `openspec/changes/` (non-archive) and ask user to select.

## 2. Validate specs

Run: `openspec validate <change-name> --strict`

If exit code != 0:

- Output the errors and warnings
- Exit with status 1 and message: "Spec validation failed. Fix the issues above and re-run `/openspec-tdd:verify`."

## 3. Detect and run tests

Auto-detect test command in order of priority:

1. Custom command from `.openspec/config.json` → `tdd.testCommand`
2. `npm test` (if `package.json` exists)
3. `pytest` (if Python project detected)
4. `go test ./...` (if `go.mod` exists)
5. `cargo test` (if `Cargo.toml` exists)

Run the detected test command.

If exit code != 0:

- Output the failing tests
- Exit with status 1 and message: "Tests failed. Fix the failing tests and re-run `/openspec-tdd:verify`."

## 4. Output result

If both pass:

```
✅ Spec validation: PASSED
✅ Test suite: PASSED

All checks passed. Ready to archive with `/openspec-tdd:finish`.
```

## 5. Error handling

- If no change is active: "No active change. Run `/openspec-tdd:start` first."
- If no test command can be detected: "Could not detect a test command. Set `tdd.testCommand` in `.openspec/config.json`."
