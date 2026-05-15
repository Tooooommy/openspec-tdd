---
description: Run code review (uses /code-review skill)
---

Execute code review using the standalone skill.

1. Ensure `./skills/code-review/SKILL.md` exists. If not, prompt user to install it.

2. Call `/code-review` (the skill). It will output report.

3. Parse output for blocking issues. If any:
   - Append each to `tasks.md` as `- [ ] [CR] issue @file:line`
   - Exit with status 1 and message: "Blocking issues found. Run /openspec-tdd:continue to fix."

4. If no blocking issues:
   - Create `.scratch/openspec-tdd/review-code-passed`
   - Output "Code review passed."

5. Optionally suggest next command: `/openspec-tdd:review-arch` or `/openspec-tdd:finish`.
