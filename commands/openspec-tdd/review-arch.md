---
description: Run architecture review using /improve-codebase-architecture
---

Execute architecture review using the standalone skill.

## 1. Pre-check

Ensure `./skills/openspec-tdd/references/review-checklist.md` exists. If not, warn and abort.

## 2. Identify current change

Read `.scratch/openspec-tdd/current-change`. If missing, scan `openspec/changes/` (non-archive) and ask user to select.

## 3. Run architecture review

Call `/improve-codebase-architecture`. It will analyze the codebase and output a report.

## 4. Parse output

Check the report for issue severity levels:

- **Critical**: Must be fixed before proceeding
- **Suggestion**: Should be evaluated
- **Optional**: Developer discretion

## 5. Handle results

### If Critical issues found:

- Append each critical issue to `tasks.md` as:
  ```
  - [ ] [ARCH] <issue description> @<file:line>
  ```
- Output: "Critical architecture issues found. Run `/openspec-tdd:continue` after fixing."
- Exit with status 1.

### If only Suggestions or Optional issues:

- List them for user awareness
- Ask user: "Accept these suggestions as-is? (y/n)"
- If no → append to `tasks.md` as above
- If yes → proceed

## 6. On pass

- Create `.scratch/openspec-tdd/review-arch-passed`
- Output: "Architecture review passed."
- Suggest next command: `/openspec-tdd:finish`

## 7. Error handling

- If `/improve-codebase-architecture` is not available, output: "The improve-codebase-architecture skill is not installed. Install it with: `npx skills@latest add mattpocock/skills`"
- If no change is active, output: "No active change. Run `/openspec-tdd:start` first."
