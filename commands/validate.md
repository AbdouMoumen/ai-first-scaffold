---
description: Validate existing AI-first infrastructure for completeness and correctness
---

# Validate Command

Check if the current project has valid AI-first infrastructure.

## What to Check

1. **AGENTS.md**
   - [ ] File exists
   - [ ] Has Commands section
   - [ ] Has Terminology section (if domain-specific)
   - [ ] Has Workflow section
   - [ ] Has Pre-Commit Requirements
   - [ ] Has Agents table
   - [ ] Has Skills table

2. **PROGRESS.md**
   - [ ] File exists
   - [ ] Has valid YAML frontmatter (sprint, sprint_name, active_work_unit, last_updated)
   - [ ] Has Sprint section
   - [ ] Has Workstream Status table

3. **agents/ directory**
   - [ ] Directory exists
   - [ ] At least core agents present (program-manager, architect, testing, auditor)
   - [ ] Each agent has valid frontmatter (name, role, expertise, can-implement)
   - [ ] Each agent has Constraints section

4. **skills/ directory**
   - [ ] Directory exists (`.claude/skills/` or `.github/skills/`)
   - [ ] Core skills present (status, implement, prepare-work)
   - [ ] Each skill has valid frontmatter (name, description)

## Output Format

```markdown
## 🔍 AI-First Infrastructure Validation

### AGENTS.md
✅ Found and valid
- Commands: ✅
- Terminology: ✅
- Workflow: ✅
- Pre-Commit: ✅

### PROGRESS.md
✅ Found and valid
- Frontmatter: ✅
- Sprint section: ✅

### Agents
✅ 4 agents found
- program-manager.md: ✅
- architect.md: ✅
- testing.md: ✅
- auditor.md: ✅

### Skills
✅ 3 skills found
- status: ✅
- implement: ✅
- prepare-work: ✅

### Summary
✅ All infrastructure valid

OR

⚠️ Issues found:
- [ ] Missing: agents/testing.md
- [ ] Invalid frontmatter: agents/frontend.md
```

## Example Usage

```
/ai-first:validate
```
