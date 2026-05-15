# Architecture Review Checklist

This checklist serves as a reference for the `/improve-codebase-architecture` skill during review and can also be used for manual review.

## Interface Design

- [ ] Are public APIs clear and self-explanatory?
- [ ] Are there dependencies on internal implementation details? (e.g., exporting private types)
- [ ] Does it follow the Interface Segregation Principle (not forcing callers to depend on unused methods)?
- [ ] Are inputs and outputs properly validated with reasonable error handling?

## Test Quality

- [ ] Do tests verify behavior rather than implementation details?
- [ ] Are there sufficient boundary condition tests (null values, invalid inputs)?
- [ ] Are there integration tests verifying component collaboration?
- [ ] Are tests independent, repeatable, and fast?

## Coverage

- [ ] Is core business logic coverage ≥ 80%?
- [ ] Are error handling branches covered by tests?
- [ ] Is there uncovered code that needs explanation?

## Architecture Consistency

- [ ] Does the implementation follow the structure described in `design.md`?
- [ ] Are dependency directions between modules reasonable (no circular dependencies)?
- [ ] Are there violations of design principles (e.g., Single Responsibility, Open/Closed)?
- [ ] Is there unnecessary code duplication?

## Maintainability

- [ ] Is naming consistent and meaningful?
- [ ] Are there sufficient comments explaining "why" rather than "how"?
- [ ] Are configurations and constants centrally managed?
- [ ] Has unnecessary complexity been introduced?

## Security

- [ ] Are inputs validated and sanitized?
- [ ] Is sensitive data (passwords, keys) properly protected (not logged, not stored in plaintext)?
- [ ] Are there potential injection or privilege bypass risks?

## Performance

- [ ] Are there obvious inefficient operations (N+1 queries, redundant computations)?
- [ ] Is there necessary caching or async processing?

## Issue Severity Levels

- **Critical**: Must be fixed before proceeding to the next stage (e.g., security vulnerabilities, serious design flaws)
- **Suggestion**: Should be fixed but can be temporarily accepted (e.g., minor code duplication, naming inconsistencies)
- **Optional**: Left to the developer's discretion (e.g., style preferences)