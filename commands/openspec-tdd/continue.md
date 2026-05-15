---
description: Resume from where pipeline stopped
---

Resume the pipeline.

## 1. Identify current change

Read `.scratch/openspec-tdd/current-change`. If missing, scan `openspec/changes/` (non-archive) → ask user.

Also read `.scratch/openspec-tdd/branch` and compare with `git branch --show-current`. If mismatch, warn.

## 2. Determine phase

Check flags in `.scratch/openspec-tdd/`:

- `requirements-confirmed` missing → goto Phase 1
- `specs-validated` missing → goto Phase 2
- `tasks-refined` missing → goto Phase 3
- `tdd-complete` missing → goto Phase 4 (resume incomplete tasks)
- `review-code-passed` missing → goto Phase 5
- `review-arch-passed` missing → goto Phase 6
- otherwise → Phase 7

## 3. Execute phase

Execute the same logic as in `start.md` for that phase, then stop (do not auto-proceed to next phase unless user says "continue again").

After each phase completion, prompt: "Phase X complete. Run `/openspec-tdd:continue` to proceed."
