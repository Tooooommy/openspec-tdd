# Troubleshooting

## Common Issues

### `openspec: command not found`

Install: `npm install -g @fission-ai/openspec`

### `/tdd` not recognized

Add Matt Pocock skills: `npx skills@latest add mattpocock/skills`

### `/grill-me` or `/to-issues` not recognized

These are part of the Matt Pocock skills package. Install with: `npx skills@latest add mattpocock/skills`

### `/improve-codebase-architecture` not recognized

Also part of Matt Pocock skills. Install with: `npx skills@latest add mattpocock/skills`

### Code review finds blocking issues but tasks.md not updated

Check that `./skills/code-review/SKILL.md` is present. Manual fix: append tasks to `tasks.md` with `[CR]` prefix.

### Branch mismatch warning

Run `/openspec-tdd:reset soft` then `/openspec-tdd:start` again.

### `openspec validate` fails with "missing scenario"

Ensure each Requirement has `#### Scenario:` with GIVEN/WHEN/THEN.

### TDD task takes >10 minutes

Split task further using `/to-issues`.

## State & Recovery

### Pipeline state is corrupted

1. Run `/openspec-tdd:status` to see current state
2. If flags are inconsistent, run `/openspec-tdd:reset soft` to clear flags
3. If tasks.md is corrupted, run `/openspec-tdd:reset hard` (creates backup first)
4. If all else fails, manually delete `.scratch/openspec-tdd/` and start over

### Lost track of which phase I'm on

Run `/openspec-tdd:status` — it shows all phase flags and task progress.

### Need to switch branches mid-pipeline

1. Run `/openspec-tdd:reset soft` to clear flags
2. Switch branches with `git checkout <branch>`
3. Run `/openspec-tdd:start` or `/openspec-tdd:continue` on the new branch

## Skill Issues

### Skill not found after installation

Ensure skills are installed in the correct directory:
- `./skills/code-review/` (bundled with this package)
- Matt Pocock skills installed globally via `npx skills@latest add mattpocock/skills`

### `/code-review` outputs nothing

Check that there are uncommitted changes: `git diff --name-only HEAD`. If no changes, the review has nothing to analyze.

## Configuration

### Custom test command not working

Set `tdd.testCommand` in `.openspec/config.json`:

```json
{
  "tdd": {
    "testCommand": "npm run test:ci"
  }
}
```

### Task time limit too short/long

Adjust `tdd.taskTimeMinutes` in `.openspec/config.json`:

```json
{
  "tdd": {
    "taskTimeMinutes": 15
  }
}
```

## Archive Issues

### Archive fails with merge conflicts

Conflicts in `specs/` directory. Resolve manually, then re-run `/openspec-tdd:finish`.

### Archive says "no changes to archive"

Ensure `openspec/changes/<change-name>/` exists and has content. Run `/openspec-tdd:status` to verify.

## Getting Help

- Run `/openspec-tdd:help` for command reference
- Run `/openspec-tdd:status` to see current state
- Check `.scratch/openspec-tdd/` for state files