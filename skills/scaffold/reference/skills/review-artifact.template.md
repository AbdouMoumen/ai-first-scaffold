````skill
---
name: review-artifact
description: Reviews artifacts by auto-detecting type and routing to domain agents - produces severity-ranked feedback. Returns structured findings (critical/major/minor).
allowed-tools: Read, Grep, Glob
---

# Review Artifact Skill

Conduct structured reviews of code, documentation, or design artifacts.

## Triggers

| Trigger | Action |
|---------|--------|
| `/review-artifact` | Review files from context |
| "review this code" | Review specified code |
| "review this document" | Review documentation |
| Called by `implement` | Mandatory review phase |

## Workflow

### Phase 1: Detect Artifact Type

| Patterns | Type | Reviewers |
|----------|------|-----------|
| `.ts`, `.tsx`, `.js` | Code | Testing, Auditor |
| `.md` in `docs/` | Documentation | Architect, Auditor |
| `agents/*.md` | Agent definition | AI-Prompts, Auditor |
| `SKILL.md` | Skill definition | AI-Prompts, Auditor |
| `.css`, `.scss` | Styles | Frontend, Design |
| SQL, migrations | Database | Backend, Architect |

### Phase 2: Load Reviewers

Read relevant agent instructions to apply their review criteria.

### Phase 3: Review

For each reviewer perspective:

1. **Check against agent's criteria**
2. **Identify issues with severity**
3. **Provide specific fix recommendations**

**Severity Levels:**

| Level | Criteria | Action |
|-------|----------|--------|
| 🔴 Critical | Blocks release, security risk | Must fix |
| 🟠 Major | Significant quality issue | Should fix |
| 🟡 Minor | Code smell, style issue | Fix when convenient |
| 🔵 Info | Suggestion, best practice | Consider |

### Phase 4: Verdict

| Findings | Verdict |
|----------|---------|
| No critical/major | ✅ Accept |
| Minor issues only | ⚠️ Accept with changes |
| Major issues | 🔄 Revise |
| Critical issues | ❌ Reject |

## Output Format

```markdown
## Review: [Artifact Name]

**Type**: Code | Documentation | Agent | Skill
**Reviewers**: [Agent list]
**Verdict**: ✅ Accept | ⚠️ Accept with changes | 🔄 Revise | ❌ Reject

### Summary
| Severity | Count |
|----------|-------|
| 🔴 Critical | 0 |
| 🟠 Major | 1 |
| 🟡 Minor | 2 |
| 🔵 Info | 1 |

### Findings

#### 🟠 Major

**M1: [Title]**
- **Location**: `file.ts:L42`
- **Issue**: [Description]
- **Fix**: [Recommendation]

#### 🟡 Minor

**m1: [Title]**
- **Location**: `file.ts:L10`
- **Issue**: [Description]
- **Fix**: [Recommendation]

### Verdict Rationale
[Why this verdict was chosen]

### Next Steps
1. [Action to take]
```

## Integration

- **Called by**: `implement` (Phase 3, mandatory)
- **Invokes**: Domain agent perspectives (virtual)
````
