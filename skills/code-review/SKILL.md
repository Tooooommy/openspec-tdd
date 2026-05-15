---
name: code-review
description: |
  Execute a thorough, structured code review on the current changes. 
  Use when user asks for "code review", "review my code", "check for issues", 
  "run security audit", or when completing a feature.
  Triggers: "review this code", "find bugs", "is this code ready?".
  Part of openspec-tdd pipeline but can be used standalone.
license: MIT
version: 2.0.0
metadata:
  author: openspec-tdd
---

# Code Review Skill (Matt Pocock Style)

Run a rigorous, actionable code review. Focus on **blocking issues** first, then warnings.

## Input Sources

Determine what to review (in order):

1. If `$CHANGE_NAME` environment variable or `.scratch/openspec-tdd/current-change` exists, use that change's files.
2. If in Git: `git diff --name-only HEAD` (uncommitted) or `git diff --name-only main...HEAD` (branch).
3. Else ask user for file paths.

## The Review Process

### Step 1: Load Checklist

Read `./review-checklist.md` (same directory). This contains all review criteria.

### Step 2: Scan Files

For each file in scope, apply checklist items. Record findings with:

- File path + line number(s)
- Severity: `BLOCKING` / `WARNING` / `INFO`
- Description
- Suggested fix

**BLOCKING criteria** (must fix before merge):

- Hardcoded secrets, passwords, tokens
- SQL injection, XSS, path traversal
- Logging sensitive data (passwords, tokens, PII)
- No error handling for critical paths
- Missing null/undefined checks that cause runtime errors

### Step 3: Generate Report

Use this exact format:

```markdown
📋 Code Review Report

## ✅ Passed

- [Category]: specific items that passed

## ⚠️ Warnings (Should Fix)

- [Severity] `file:line` - Description

## ❌ Blocking Issues (Must Fix)

- [Severity] `file:line` - Description

## 📊 Summary

- Blocking issues: X
- Warnings: Y
- **Verdict**: PASS / FAIL
```

### Step 4: Provide Fix Recommendations

For each blocking/warning issue, give:

```markdown
## 🔧 Fix Recommendations

### `file:line` - Issue

**Fix**: concrete steps
**Example**: before/after code
```

## Integration with openspec-tdd

When called from openspec-tdd pipeline:

- If **Blocking Issues** found:
  - Append each as task to `tasks.md`: `- [ ] [CR] description @file:line`
  - Do **not** create `review-code-passed` flag
  - Exit with error code 1
- If **only Warnings** or PASS:
  - Create `.scratch/openspec-tdd/review-code-passed`
  - Exit with code 0

## Standalone Usage

Just run `/code-review` - it will analyze current changes and output report.

## Principles

- **Actionable** over descriptive
- **Precise** (file:line)
- **Constructive** (always suggest fix)
- **Fast** (no lengthy philosophy)
