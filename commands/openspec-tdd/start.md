---
description: Start full SDD+TDD+CodeReview pipeline (7 phases)
arguments:
  - name: description
    required: false
---

# Start OpenSpec-TDD Pipeline

## 0. Pre-flight Checks

### 0.1 OpenSpec CLI

Run `openspec --version`, require >=0.9.0.

If not installed: "OpenSpec CLI not found. Install with: `npm install -g @fission-ai/openspec`"

### 0.2 Required Skills

Verify the following skills are available. If any are missing, output install instructions and abort.

| Skill                             | Check                                           | Install Command                           |
| --------------------------------- | ----------------------------------------------- | ----------------------------------------- |
| `/grill-me` or `/grill-with-docs` | Check if skill is registered                    | `npx skills@latest add mattpocock/skills` |
| `/tdd`                            | Check if skill is registered                    | `npx skills@latest add mattpocock/skills` |
| `/to-issues`                      | Check if skill is registered                    | `npx skills@latest add mattpocock/skills` |
| `/improve-codebase-architecture`  | Check if skill is registered                    | `npx skills@latest add mattpocock/skills` |
| `/code-review`                    | Check if `./skills/code-review/SKILL.md` exists | Ensure `skills/code-review/` is present   |

If any skill is missing:

```
❌ Missing required skills:

   - /tdd (install: npx skills@latest add mattpocock/skills)
   - /to-issues (install: npx skills@latest add mattpocock/skills)

   Install missing skills and re-run `/openspec-tdd:start`.
```

Abort.

### 0.3 Git branch

Capture `git branch --show-current` → store in `.scratch/openspec-tdd/branch`.

If not in a git repo: "Not in a git repository. Initialize one with `git init` first."

### 0.4 Check active change

If `.scratch/openspec-tdd/current-change` exists:

```
⚠️  An active change already exists: <change-name>

   Run `/openspec-tdd:reset soft` to clear it, or `/openspec-tdd:continue` to resume.
```

Abort.

## 1. Generate Change Name

If `$description` provided, convert to kebab-case (e.g., "add user login" → `add-user-login`).  
If name exists in `openspec/changes/`, append `-2`, `-3`, etc.

Run: `openspec new <change-name>`

Store change name in `.scratch/openspec-tdd/current-change` and branch name.

## 2. Phase 1: Requirements

Call `/grill-me` with the description.

After completion, save to `.scratch/openspec-tdd/requirements.md`.  
Wait for user "confirm" (explicit).

Create flag `.scratch/openspec-tdd/requirements-confirmed`.

## 3. Phase 2: Spec Generation

- Write `proposal.md` (Why/What/Scope/Constraints)
- Run `openspec instructions specs --json` → create `specs/*.md` with `### Requirement:` and `#### Scenario:` (GIVEN/WHEN/THEN)
- Run `openspec instructions design --json` → create `design.md`
- Run `openspec instructions tasks --json` → create `tasks.md`
- Validate: `openspec validate <change-name> --strict` – must pass

Wait for user "confirm". Create `.scratch/openspec-tdd/specs-validated`.

## 4. Phase 3: Task Breakdown

Call `/to-issues` on `tasks.md` (Matt Pocock skill).  
Ensure each task is 5-10 min TDDable.

Wait for user "confirm". Create `.scratch/openspec-tdd/tasks-refined`.

## 5. Phase 4: TDD Implementation

Iterate over all `[ ]` tasks in `tasks.md`:

For each task:

- Call `/tdd` with task description
- Wait for completion, tests green
- Mark `[x]` in `tasks.md`

After all done → `.scratch/openspec-tdd/tdd-complete`.

## 6. Phase 5: Code Review

Call `/code-review` (the standalone skill).  
If it returns blocking issues → append to `tasks.md` → exit with instruction to run `/openspec-tdd:continue`.  
If pass → create `.scratch/openspec-tdd/review-code-passed`.

## 7. Phase 6: Architecture Review

Call `/improve-codebase-architecture`.  
If issues found → append to `tasks.md` → exit with instruction to continue.  
If pass → create `.scratch/openspec-tdd/review-arch-passed`.

## 8. Phase 7: Verify & Archive

- `openspec validate <change-name> --strict`
- Run test command (auto-detected: npm test, pytest, go test, cargo test, or custom from config)
- `openspec archive <change-name> --yes`

Remove `.scratch/openspec-tdd/current-change` (keep flags).  
Output completion summary.

**If any step fails, stop and show error with fix instructions.**
