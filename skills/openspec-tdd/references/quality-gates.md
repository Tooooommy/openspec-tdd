# Quality Gates Checklist

This document defines the exit conditions that must be met at each stage of the OpenSpec-TDD pipeline. The AI must verify all gates before proceeding to the next stage.

## Stage 1: Requirements Alignment Exit Conditions

- [ ] All questions from `/grill-me` have been explicitly answered
- [ ] All ambiguities have been resolved (no vague terms like "maybe", "probably")
- [ ] The user has explicitly confirmed that the requirements are correctly understood
- [ ] `.scratch/openspec-tdd/requirements.md` has been generated and is complete

## Stage 2: Spec Generation Exit Conditions

- [ ] `proposal.md` contains all four sections: "Background", "Goals", "Scope", "Constraints"
- [ ] Each requirement in `specs/*.md` uses the `### Requirement:` heading
- [ ] Each requirement has at least one `#### Scenario:` using GIVEN/WHEN/THEN format
- [ ] Each scenario uses normative keywords such as SHALL, MUST
- [ ] `design.md` includes architecture decisions, data model, error handling, and risk assessment
- [ ] `tasks.md` has been generated (even if coarse-grained)
- [ ] `openspec validate --strict` exits with code 0, no errors or warnings

## Stage 3: Task Breakdown Exit Conditions

- [ ] Each task can complete a full TDD cycle within 5–10 minutes
- [ ] No circular dependencies between tasks
- [ ] Each task has a clear "definition of done" (e.g., tests pass, interface conforms to spec)
- [ ] The user has confirmed the task breakdown is reasonable

## Stage 4: TDD Implementation Exit Conditions

- [ ] All tasks in `tasks.md` are marked `[x]`
- [ ] All tests (unit + integration) pass
- [ ] Test coverage (core logic) ≥ 80% (can be verified later by `/improve-codebase-architecture`)
- [ ] No TDD cycles were skipped (i.e., no evidence of "implement first, test later")

## Stage 5: Code Review Exit Conditions

- [ ] `/code-review` report has no "Blocking" level issues
- [ ] All "Warning" level issues have been evaluated, explicitly accepted or fixed
- [ ] No hardcoded secrets, SQL injection, XSS, or path traversal vulnerabilities
- [ ] New code has corresponding tests
- [ ] Error handling covers critical paths
- [ ] `.scratch/openspec-tdd/review-code-passed` flag exists

## Stage 6: Architecture Review Exit Conditions

- [ ] `/improve-codebase-architecture` report has no "Critical" level issues
- [ ] All "Suggestion" level issues have been evaluated, explicitly accepted or fixed
- [ ] Core logic test coverage ≥ 80%
- [ ] Interface design does not depend on implementation details
- [ ] Code structure is consistent with `design.md`

## Stage 7: Final Validation & Archive Exit Conditions

- [ ] `openspec validate --strict` passes again
- [ ] Full test suite (e.g., `npm test`) exits with code 0
- [ ] `openspec archive` executes successfully with no errors
- [ ] Change directory has been moved from `changes/` to `changes/archive/`
- [ ] Main `specs/` directory has been updated
