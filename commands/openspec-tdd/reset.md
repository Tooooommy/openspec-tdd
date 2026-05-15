---
description: Reset current change (clear flags, mark all tasks incomplete)
arguments:
  - name: level
    description: "soft" (just flags) or "hard" (also reset tasks.md checkboxes)
    default: soft
---

Reset the pipeline state for the current change.

## 1. Identify current change

Read `.scratch/openspec-tdd/current-change`. If missing, scan `openspec/changes/` (non-archive) and ask user to select.

## 2. Confirm reset

Show what will be affected:

```
Reset level: <soft|hard>
Change:      <change-name>

Soft reset will:
  - Delete all phase flags (requirements-confirmed, specs-validated, etc.)
  - Keep current-change and branch info
  - Keep tasks.md as-is

Hard reset will:
  - Everything in soft reset
  - Reset all [x] → [ ] in tasks.md
```

Ask: "Proceed with reset? (y/n)"

If no → abort.

## 3. Execute soft reset

Delete all files in `.scratch/openspec-tdd/` **except**:

- `current-change`
- `branch`

Files to delete:

- `requirements-confirmed`
- `specs-validated`
- `tasks-refined`
- `tdd-complete`
- `review-code-passed`
- `review-arch-passed`

## 4. Execute hard reset (if level=hard)

After soft reset, also:

1. **Backup tasks.md**: Copy `openspec/changes/<change-name>/tasks.md` to `openspec/changes/<change-name>/tasks.md.bak`
2. **Reset checkboxes**: Replace all `- [x]` with `- [ ]` in `tasks.md`

## 5. Output

```
✅ Reset complete (soft)

   All phase flags cleared.
   Change "<change-name>" is ready for a fresh start.

   Run `/openspec-tdd:start` to begin again.
```

## 6. Error handling

- If no change is active: "No active change to reset."
- If backup fails (hard reset): warn but continue with checkbox reset.
- If `tasks.md` doesn't exist (hard reset): skip checkbox reset, warn user.
