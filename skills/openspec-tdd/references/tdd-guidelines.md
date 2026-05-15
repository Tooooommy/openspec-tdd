# TDD Implementation Guidelines

This document provides supplementary guidance for the `/tdd` skill to ensure consistent and high-quality behavior within the OpenSpec-TDD workflow.

## Strict Red-Green-Refactor Enforcement

In OpenSpec-TDD, each task must go through a complete TDD cycle. **Do not** implement multiple tasks at once.

### RED Phase (Write a Failing Test)

- The test must only **fail**, not produce a compilation error (unless language constraints apply)
- The test only verifies the behavior of the current task, without depending on unimplemented tasks
- Tests exercise the **public interface** (API, function signatures), not private methods
- Use the project's test framework and assertion library

### Verify RED

- Run the test and confirm it fails (red)
- If the test unexpectedly passes, the requirement is already implemented or the test is flawed — the test must be revised

### GREEN Phase (Write Minimal Code to Pass)

- Write only enough code to make the current test pass
- Hardcoding and simple implementations are allowed; do not prematurely consider extensibility
- Do not implement extra functionality (YAGNI principle)

### Verify GREEN

- Run all tests (not just the current one) and confirm they all pass
- If other tests fail, fix the implementation (a contract may have been violated)

### REFACTOR Phase (Refactor)

- Improve code structure under the protection of tests: eliminate duplication, improve readability, extract constants/functions
- Do not change external behavior (all tests must still pass)
- Test code itself may also be improved

### Verify REFACTOR

- Run all tests again to ensure the refactoring introduced no errors

## Vertical Slices vs. Horizontal Layers

OpenSpec-TDD recommends a **vertical slice** approach: each task completes a small, full feature from interface to storage, rather than completing all data layers first, then all business layers.

Example (user login feature):

- ✅ Vertical: Implement "email format validation" (from input validation to returning error messages)
- ✅ Vertical: Implement "password hash comparison" (from receiving the request to calling the crypto library)
- ❌ Horizontal: Implement all data models first, then all service layers

## Test Naming Conventions

- Test names should describe behavior, not implementation: `test_email_validation_rejects_missing_at_symbol`
- Use the Given-When-Then structure to organize test code

## Common Anti-Patterns and Corrections

| Anti-Pattern               | Symptom                                       | Correction                                                  |
| -------------------------- | --------------------------------------------- | ----------------------------------------------------------- |
| Skipping RED               | Writing implementation code first, then tests | Delete implementation code, start from RED                  |
| Mega GREEN                 | Implementing full functionality in one go     | Break into multiple TDD tasks, complete one by one          |
| Testing Implementation     | Testing private methods or mocking internals  | Refactor tests to verify only via public interface          |
| Skipping Refactor          | Not cleaning up code after functionality done | Enforce REFACTOR after GREEN                                |
| Depending on Unimplemented | Tests mock modules that don't exist yet       | Implement the dependency first, or use simpler test doubles |
