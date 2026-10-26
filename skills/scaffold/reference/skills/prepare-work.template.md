---
name: prepare-work
description: Transforms issues or requests into implementation plans - explores codebase, assesses complexity, generates structured plan with test scenarios.
allowed-tools: Read, Grep, Glob, Bash
---

# Prepare Work Skill

Transform a request into a validated implementation plan.

## Triggers

| Trigger                        | Action                       |
| ------------------------------ | ---------------------------- |
| `prepare-work #42`             | Create plan for issue        |
| `prepare-work "add feature X"` | Create plan from description |
| "plan this work"               | Create plan from context     |

## Workflow

### Phase 1: Understand

1. **Parse input**: Extract the core requirement
2. **Read context**:
   - Issue description (if issue number)
   - Related documentation
   - Existing patterns

### Phase 2: Explore

1. **Find related code**:
   ```bash
   grep -r "related_pattern" src/
   ```
2. **Identify patterns**: Read existing implementations
3. **Map dependencies**: List files that will be affected

### Phase 3: Plan

1. **Assess complexity**:

| Signals                              | Complexity |
| ------------------------------------ | ---------- |
| Single file, clear pattern           | Low        |
| Multiple files, established patterns | Medium     |
| New patterns, many files, unknowns   | High       |

2. **Generate plan**:

```markdown
# Implementation Plan: [Title]

## Summary

[One-sentence description]

## Scope

- **Domain**: frontend | backend | testing
- **Complexity**: Low | Medium | High
- **Estimated effort**: Xh

## What to Build

1. [Deliverable 1]
2. [Deliverable 2]

## Files to Modify

| File           | Change       |
| -------------- | ------------ |
| `path/file.ts` | Add function |

## Patterns to Follow

- `existing/pattern.ts` — [what to learn]

## Test Scenarios

| Priority | Scenario   | Expected           |
| -------- | ---------- | ------------------ |
| P0       | Happy path | Works correctly    |
| P0       | Error case | Handles gracefully |
| P1       | Edge case  | Behaves correctly  |

## Open Questions

- [ ] [Any unknowns needing human input]

## Dependencies

- [Any blockers or prerequisites]
```

### Phase 4: Validate

**Before marking complete, verify:**

- [ ] Plan addresses the original request
- [ ] All affected files identified
- [ ] Test scenarios cover key paths
- [ ] Complexity assessment is realistic
- [ ] No unresolved blockers

## Output

**Check AGENTS.md for `plan_output` preference:**

| Setting                    | Action                                         |
| -------------------------- | ---------------------------------------------- |
| `file` (default for teams) | Write to `.plans/sprint-N/workstream-title.md` |
| `inline`                   | Return plan markdown in conversation           |

**If writing to file:**

```bash
mkdir -p .plans/sprint-{{current_sprint}}
# Write plan to .plans/sprint-N/w{workstream}-{slug}.md
```

Report:

```markdown
✅ Plan created: `.plans/sprint-1/w2-feature-name.md`

**To execute:**

- Open the plan file
- Run `/implement` or "implement this plan"
```

**If inline:**
Return the plan markdown directly, ready for `implement` skill to execute.
