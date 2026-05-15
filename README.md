# OpenSpec-TDD + Code Review

Complete SDD (Spec-Driven Development) + TDD (Test-Driven Development) + Code Review pipeline, integrating Matt Pocock-style skills.

## Installation

1. Install OpenSpec CLI:

   ```bash
   npm install -g @fission-ai/openspec
   ```

2. Add Matt Pocock Skills:

   ```bash
   npx skills@latest add mattpocock/skills
   ```

3. Copy this skill package to the directory:
   - `./skills/code-review/`
   - `./skills/openspec-tdd/`
   - `./commands/openspec-tdd/`

4. Initialize OpenSpec:
   ```bash
   openspec init
   ```

## Quick Start

```bash
# Wizard mode (recommended for beginners)
/openspec-tdd:wizard

# Start directly
/openspec-tdd:start "Add user login feature"

# Resume after interruption
/openspec-tdd:continue

# Check progress
/openspec-tdd:status

# Standalone code review
/openspec-tdd:review-code

# Finalize and archive
/openspec-tdd:finish
```

## Command List

| Command                      | Description                   |
| ---------------------------- | ----------------------------- |
| `/openspec-tdd:start "desc"` | Full 7-phase pipeline         |
| `/openspec-tdd:continue`     | Resume from last interruption |
| `/openspec-tdd:implement 3`  | Run TDD on task 3 directly    |
| `/openspec-tdd:review-code`  | Code review (checklist-based) |
| `/openspec-tdd:review-arch`  | Architecture review           |
| `/openspec-tdd:verify`       | Validate specs + tests        |
| `/openspec-tdd:finish`       | Final validation and archive  |
| `/openspec-tdd:status`       | Show progress and branch      |
| `/openspec-tdd:reset soft`   | Reset flags                   |
| `/openspec-tdd:wizard`       | Interactive wizard            |
| `/openspec-tdd:help`         | List all commands             |

## Workflow Diagram

```
start → Requirements → Specs → Tasks → TDD → Code Review → Architecture Review → Verify & Archive
        (grill-me)    (specs)  (to-issues) (tdd)  (code-review)  (improve-arch)
```

## Troubleshooting

If you encounter issues, check `references/troubleshooting.md` or verify:

- OpenSpec CLI >= 0.9.0
- Git branch matches the recorded branch
- Matt Pocock Skills are installed

## Custom Configuration

Override in `.openspec/config.json`:

- `testCommand` – test command
- `taskTimeMinutes` – target task duration (minutes)
- `autoConfirm` – auto-confirm gates (use with caution)

## File Structure

```
  skills/
    code-review/          # Standalone review skill
    openspec-tdd/         # Pipeline container
  commands/openspec-tdd/  # All command implementations
.openspec/config.json     # Optional configuration
```

## Contributing

Improvements to checklists or command logic are welcome. Keep the Matt Pocock style: concise, executable, no fluff.
