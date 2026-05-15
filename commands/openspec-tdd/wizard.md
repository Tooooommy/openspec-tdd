---
description: Interactive wizard that guides through the pipeline step by step
---

Run an interactive Q&A session to guide the user through the full pipeline.

## 1. Welcome

```
🧙 OpenSpec-TDD Wizard

   This wizard will guide you through the 7-phase pipeline:
   Requirements → Specs → Tasks → TDD → Code Review → Architecture → Archive

   You can exit at any time by saying "stop" or "exit".
   Progress is saved after each phase.
```

## 2. Gather feature description

Ask: "Describe the feature you want to build:"

Wait for user input. If empty or "exit" → abort.

## 3. Confirm plan

Show summary:

```
📋 Plan Summary

   Feature: <user-description>
   Pipeline: Full 7-phase (Requirements → Archive)

   I will now run `/openspec-tdd:start` with this description.
```

Ask: "Proceed? (y/n)"

If no → ask: "Would you like to revise the description? (y/n)"

- If yes → go back to step 2
- If no → abort

## 4. Execute Phase 1: Requirements

Execute `/openspec-tdd:start` logic for Phase 1 (call `/grill-me`, save requirements).

After completion, ask: "Phase 1 (Requirements) complete. Continue to Phase 2 (Spec Generation)? (y/n)"

If no → save state and exit with: "Progress saved. Resume with `/openspec-tdd:continue`."

## 5. Execute Phase 2: Spec Generation

Execute Phase 2 logic (generate specs, validate).

After completion, ask: "Phase 2 (Specs) complete. Continue to Phase 3 (Task Breakdown)? (y/n)"

If no → save state and exit.

## 6. Execute Phase 3: Task Breakdown

Execute Phase 3 logic (call `/to-issues`, refine tasks).

After completion, ask: "Phase 3 (Tasks) complete. Continue to Phase 4 (TDD Implementation)? (y/n)"

If no → save state and exit.

## 7. Execute Phase 4: TDD Implementation

Execute Phase 4 logic (iterate tasks with `/tdd`).

After completion, ask: "Phase 4 (TDD) complete. Continue to Phase 5 (Code Review)? (y/n)"

If no → save state and exit.

## 8. Execute Phase 5: Code Review

Execute Phase 5 logic (call `/code-review`).

If blocking issues found:

- Show issues
- Ask: "Blocking issues found. Fix them now? (y/n)"
- If yes → wait for user to fix, then re-run review
- If no → save state and exit

After pass, ask: "Phase 5 (Code Review) complete. Continue to Phase 6 (Architecture Review)? (y/n)"

If no → save state and exit.

## 9. Execute Phase 6: Architecture Review

Execute Phase 6 logic (call `/improve-codebase-architecture`).

If critical issues found:

- Show issues
- Ask: "Critical issues found. Fix them now? (y/n)"
- If yes → wait for user to fix, then re-run review
- If no → save state and exit

After pass, ask: "Phase 6 (Architecture) complete. Continue to Phase 7 (Final Archive)? (y/n)"

If no → save state and exit.

## 10. Execute Phase 7: Verify & Archive

Execute Phase 7 logic (validate, test, archive).

Output completion summary:

```
🎉 Pipeline Complete!

   Change: <change-name>
   Phases: 7/7 completed
   Archived: ✅

   Thank you for using OpenSpec-TDD!
```

## 11. Error recovery

If any phase fails:

- Show the error
- Ask: "An error occurred. Retry this phase? (y/n)"
- If yes → re-execute the phase
- If no → save state and exit with: "Progress saved. Resume with `/openspec-tdd:continue`."

## 12. State persistence

After each phase, ensure flags are written to `.scratch/openspec-tdd/` so `/openspec-tdd:continue` can resume correctly.
