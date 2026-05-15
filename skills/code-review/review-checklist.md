# Code Review Checklist

## 1. Naming & Readability

- [ ] Variables/functions/classes clear, follow conventions
- [ ] No magic numbers/strings (use constants)
- [ ] Functions ≤30 lines (or justified)
- [ ] Comments explain "why", not "what"

## 2. Duplication & Abstraction

- [ ] No duplicate code (DRY)
- [ ] Reusable logic extracted

## 3. Error Handling

- [ ] All exceptions caught or returned
- [ ] Error messages clear, no internal leaks
- [ ] Critical logs recorded (no sensitive data)

## 4. Boundary Conditions

- [ ] Null/undefined handled
- [ ] Empty collections handled
- [ ] Numeric boundaries considered

## 5. Security (BLOCKING if any)

- [ ] No hardcoded secrets
- [ ] No SQL injection (use parameterized queries)
- [ ] No XSS (escape output)
- [ ] No sensitive data in logs
- [ ] No path traversal

## 6. Test Quality

- [ ] New code has tests
- [ ] Tests test behavior, not implementation
- [ ] Edge cases covered

## 7. Performance

- [ ] No unnecessary loops/queries
- [ ] No memory leaks (cleanup listeners/timers)
