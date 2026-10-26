---
name: status
description: Consolidates project status from PROGRESS.md and git state - detects inconsistencies and validates tracking. Returns structured data for other skills or manual use.
allowed-tools: Bash, Read, Grep, Glob, Write
---

# status

Consolidate and validate project tracking state.

## Trigger

- **Manual**: `/status` or "what's the project status?"
- **Programmatic**: Called by other skills for context

## Constraints

- **Read-mostly**: Only write to fix critical inconsistencies
- **Report, don't assume**: Flag ambiguities for human decision
- **Fast execution**: Scan files, don't deep-analyze code
- **Structured output**: JSON-friendly format for skill consumers

## Workflow

### Phase 1: Read Tracking Files

1. **PROGRESS.md** — Current sprint, active work unit
   - Parse front-matter: sprint, sprint_name, active_work_unit, last_updated
   - Parse completed work units list
   - Parse blockers section

2. **Git status** — Uncommitted changes
   - Run `git status --short`
   - Identify modified, added, deleted files

3. **Git log** — Recent completions
   - Run `git log --oneline -10`
   - Check if commits match expected work

### Phase 2: Detect Inconsistencies

| Check | Inconsistency Pattern |
|-------|----------------------|
| Active work vs git | `active_work_unit != null` but `git status` clean |
| Orphaned changes | Modified files but no active_work_unit |
| Stale timestamp | `last_updated` > 7 days old with active work |

**Severity Levels:**
- 🔴 **Critical**: Blocks new work
- 🟡 **Warning**: Should fix but not blocking
- 🔵 **Info**: Minor inconsistency

### Phase 3: Auto-Fix (Safe Only)

Only fix these:

1. **Stale active_work_unit with clean git**:
   - Set `active_work_unit = null`
   - Note: "Auto-cleared stale active work unit"

2. **Outdated timestamp**:
   - Update `last_updated = today`

## Output Format

### For Human (Markdown)

```markdown
## 📊 Project Status

**Sprint**: {sprint} ({sprint_name})
**Status**: {✅ Complete | 🔄 In Progress | ⏸️ Blocked}

### Active Work
{active_work_unit or "None"}

### Git State
- {modified_files_count} modified files
- Last commit: {hash} - {message}

### Inconsistencies
{issues or "✅ All tracking files consistent"}
```

### For Skills (JSON)

```json
{
  "sprint": { "number": 1, "name": "Setup", "status": "in-progress" },
  "active_work": { "unit": null, "issue": null },
  "git": {
    "modified_files": 0,
    "last_commit": { "hash": "abc123", "message": "..." }
  },
  "inconsistencies": [],
  "auto_fixes": []
}
```

## Integration

- **Called by**: `implement`, `next-work-item`
- **Calls**: None (leaf skill)
