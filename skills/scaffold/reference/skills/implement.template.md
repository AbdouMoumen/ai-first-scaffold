---
name: implement
description: Executes implementation plans - implements with tests, review, and commit. Use for feature work or bug fixes.
allowed-tools: Terminal, Read, Write, Edit, Grep, Glob
---

# Implement Skill

Execute implementation: implement → test → review → commit.

## Triggers

| Trigger | Action |
|---------|--------|
| `implement this` | Execute from context |
| `implement #42` | Implement issue |
| `implement --inline "fix typo"` | Quick fix without full plan |

## Critical Rules

| Don't | Instead |
|-------|---------|
| Skip review | **Mandatory** — call review-artifact |
| Skip tests | **Mandatory** — write tests |
| Mix domains | One task = one domain |

## Workflow

### Phase 1: Setup

1. **Load context**: Read relevant agent instructions based on domain
2. **Create todo list**:
   - [ ] Phase 1: Setup
   - [ ] Phase 2: Implementation + Tests
   - [ ] Phase 3: Review (MANDATORY)
   - [ ] Phase 4: Commit

### Phase 2: Implementation

1. **Read patterns**: Follow existing conventions
2. **Implement changes**: Build the feature
3. **Write tests**: Cover key scenarios

| Change Type | Required Tests |
|-------------|----------------|
| Utility function | Unit tests |
| UI component | Component tests + a11y |
| API/Service | Integration tests |
| Bug fix | Regression test |

### Phase 3: Review

> ⚠️ **MANDATORY** — Cannot skip this phase.

1. **Self-review**:
   ```bash
   {{PRE_COMMIT_COMMANDS}}
   ```
   All must pass before proceeding.

2. **Call review-artifact**:
   ```
   /review-artifact

   Files:
   - `path/to/changed/file`
   - `tests/file.test`
   ```

3. **Handle verdict**:

| Verdict | Action |
|---------|--------|
| Accept ✅ | Proceed to Phase 4 |
| Accept with changes ⚠️ | Make fixes, re-verify |
| Revise 🔄 | Fix issues, re-review (max 3x) |
| Reject ❌ | Stop, report to human |

### Phase 4: Commit

```bash
git add .
git commit -m "<type>(<domain>): <description>

- <change 1>
- <change 2>"
```

## Inline Mode (`--inline`)

For trivial fixes:
1. Make the change
2. Run lint + tests
3. Commit directly

Skip review for changes < 10 lines with no logic change.

## Error Handling

| Error | Action |
|-------|--------|
| Build fails | Fix errors, retry |
| Tests fail | Fix tests or implementation |
| Review rejects | Fix issues, re-review (max 3x) |
| Stuck after 3 reviews | Stop, report to human |
