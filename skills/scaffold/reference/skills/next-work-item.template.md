---
name: next-work-item
description: Analyzes project plan and GitHub issues to identify ready work - parses issue dependencies, checks blocker status, detects file conflicts. Returns prioritized list with parallelization strategy. Use when deciding what to work on next.
allowed-tools: Bash, Read, Grep, Glob, Write
---

# next-work-item

Analyze the project plan and determine which work items can be started next, identifying parallelization opportunities.

## Trigger

Manual: `/next` or "what should I work on next?" or "what's ready to start?"

## Constraints (Read First)

- **Never start work automatically** — only identify and recommend
- **Respect dependencies** — blocked items cannot start until dependencies complete
- **Consult experts** — use PM agent for priority, Architect for technical dependencies
- **No assumptions** — if status is unclear, ask user to confirm
- **Explain reasoning** — always justify why items are ready and how they can be parallelized

## Workflow

### Phase 1: Analyze Current State

1. **Get consolidated status** (FIRST):
   - Call status skill → returns current sprint, active work, inconsistencies
   - If critical inconsistencies detected → report to user, do not proceed
   - If active_work_unit exists → warn user about starting concurrent work

2. **Read project documentation**:
   - `PROGRESS.md` — Current sprint status, completed items
   - `docs/process/project-plan.md` — Sprint breakdown, workstreams, dependencies

3. **Consult PM Agent**:
   - Which workstream should be prioritized?
   - Are there any blockers or waiting items?
   - What's the current sprint focus?

4. **Consult Architect Agent**:
   - Which work items have all dependencies complete?
   - Which items would cause conflicts if run in parallel?
   - Are there any technical risks or prerequisites?

### Phase 1.5: Handle Sprint Boundary

**Check if sprint transition needed:**

```
IF: All workstreams = 🟢 Complete AND all success criteria = [x]
THEN:
   1. Output transition recommendation FIRST
   2. Continue to show next sprint items (don't block)
```

**Transition Recommendation Format:**

```markdown
### 🎉 Sprint {N} Complete — Transition Recommended

**Current sprint**: {sprint_name} — all workstreams complete

**To transition to Sprint {N+1}:**

1. **Update project-plan.md** (master plan):
   - Mark all Sprint {N} success criteria as `[x]` complete
   - Update Sprint {N} workstream status to ✅ Complete with dates

2. **Create sprint archive file** (separate file, NOT appended to PROGRESS.md):
```

docs/sprints/sprint-{N}.md

```
Contains: Sprint goal, final workstream status, completed work units, success criteria, completion date

3. **Reset PROGRESS.md** for new sprint:
- Update frontmatter: `sprint: {N+1}`, `sprint_name: {next_sprint_name}`, `last_updated: {today}`
- Replace entire workstream table with Sprint {N+1} workstreams from project-plan.md
- Reset success criteria from project-plan.md
- Keep Archive section as links only: `[Sprint {N}](docs/sprints/sprint-{N}.md)`

4. **Commit**: `docs: archive sprint {N}, begin sprint {N+1}`

**Next sprint preview** (from project-plan.md):
- Goal: {next sprint goal}
- Workstreams: {list from project-plan.md}

Reply: `transition` to execute these steps
- Workstreams: {list from project-plan.md}
```

**IMPORTANT**: Keep `project-plan.md` and `PROGRESS.md` in sync:

- `project-plan.md` = historical record of ALL sprints (criteria marked complete permanently)
- `PROGRESS.md` = current sprint focus (only shows active sprint details)

**Then continue with Phase 2** using next sprint's workstreams.

### Phase 2: Identify Ready Work Items

#### Step 2.1: Fetch Open Issues with Dependencies

Get all open issues:

```bash
gh issue list --state open --limit 100 --json number,title,body,labels
```

**Dependency declarations to look for** (in issue body):

- "Depends on: #123" or "Depends on: #10, #12, #15"
- "Blocked by: #45, #67"
- "Dependencies: #89"
- "Requires: #100"

#### Step 2.2: Check Dependency Status

For each dependency found, check if it's closed:

```bash
gh issue view {dependency_number} --json state
```

Build dependency graph showing which issues are ready vs blocked.

#### Step 2.3: Apply Readiness Criteria

For each work item in current sprint:

**Dependency Check**:

- [ ] All prerequisite work items completed (from project plan)
- [ ] All GitHub issue dependencies closed (from parsed metadata)
- [ ] No blocking technical dependencies (from Architect agent)
- [ ] Required infrastructure/tools available
- [ ] No active merge conflicts in affected areas

**Status**: ✅ Ready | ⏸️ Blocked | 🔄 In Progress

**Blocked reasons**:

- `dependency-open` — Has open issue dependencies
- `technical-blocker` — Technical prerequisite not met
- `human-input` — Waiting for user input
- `in-progress` — Already being worked on

### Phase 3: Determine Parallelization Strategy

Analyze file/module overlap:

| Work Item | Affected Areas | Can Parallel With | Conflicts With |
| --------- | -------------- | ----------------- | -------------- |
| Item A    | `src/auth/`    | Item B, Item C    | Item D         |
| Item B    | `src/api/`     | Item A, Item C    | —              |

**Parallel Groups**: Items that can run simultaneously without conflicts
**Sequential Groups**: Items that must run one after another

### Phase 4: Generate Recommendations

Present ready items, blocked items, and parallelization strategy.

### Phase 5: Context-Aware Next Actions

**Always end with clear next actions based on current state:**

| Context                              | Primary Action                    | Secondary Actions           |
| ------------------------------------ | --------------------------------- | --------------------------- |
| Sprint complete (all workstreams 🟢) | **Transition to next sprint**     | Then show next sprint items |
| Multiple items ready                 | Create plans for items            | Pick one to start           |
| Single item ready                    | **Plan and implement directly**   | Skip plan menu              |
| All items blocked                    | Show blockers, suggest unblocking | —                           |

**Output the appropriate call-to-action:**

```markdown
---

## ⚡ Next Action

{{IF sprint_complete}}

### 🎉 Sprint Complete — Transition Required

All workstreams are done. **Transition to Sprint {N+1} before starting new work.**

Reply: `transition` to archive Sprint {N} and set up Sprint {N+1}

{{ELSE IF single_item_ready}}

### Ready to Start: [Item Name] (#issue)

This is the only ready item. Want me to create a plan and start implementation?

Reply: `yes` to plan and implement, or `plan only` for just the plan

{{ELSE IF multiple_items_ready}}

### 📋 Create Implementation Plans?

**Ready items:**

1. [Item 1] (#N) — recommended first
2. [Item 2] (#M)
3. [Item 3] (#P)
4. All of the above

Reply with a number, or `skip` to just review

{{ELSE}}

### ⏸️ All Items Blocked

No work items are ready. See blockers above.

**To unblock:**

- [Specific action needed]
  {{END}}
```

**When user responds**, invoke appropriate skill:

- `transition` → Execute sprint transition steps
- Number or `yes` → Invoke `/prepare-work` then optionally `/implement`
- `plan only` → Just invoke `/prepare-work`

## Output Format

```markdown
## 🎯 Next Work Items

### Ready to Start Immediately

**[Work Item Name]** (#issue_number)

- **Status**: ✅ Ready (all dependencies complete)
- **Dependencies**: #35 ✅, #40 ✅ (all closed)
- **Affected areas**: `path/to/files`, `other/module`
- **Estimated effort**: [time estimate from plan]
- **Trigger**: `implement #issue_number`

### Can Run in Parallel

**Group A** (independent changes):

- [Item 1] (#N) — affects `src/auth/`, depends on: none
- [Item 2] (#M) — affects `src/ui/`, depends on: #N ✅
- [Item 3] (#P) — affects `docs/`, depends on: none

**Strategy**: These can be worked on by separate agents simultaneously.

### Blocked Items

**[Item Name]** (#issue_number) ⏸️

- **Blocked by**: #123 (API setup) — open
- **Blocker type**: dependency-open
- **ETA**: [when dependencies expected to complete]

### Dependency Graph

Ready:
#50 → [#35 ✅, #40 ✅] → Can start now

Blocked:
#42 → [#35 ✅, #38 ⏸️] → Waiting on #38

### Reasoning

[Explain dependency analysis, why these items are ready, parallel strategy rationale]

### Recommendations

1. **Best next step**: [highest priority ready item with issue number]
2. **If multiple people**: [parallel group strategy]
3. **Watch out for**: [potential risks or considerations]

---

## ⚡ Next Action

[Context-aware call-to-action from Phase 5 — this is the most important part]
```

**Output order matters:**

1. Sprint transition warning (if applicable) — **at the very end, prominent**
2. OR ready items with clear next action
3. Never bury the call-to-action in the middle

## Integration

- **Used by**: Developer/user at start of work session
- **Calls**: `status` skill (for consolidated project state), `prepare-work` skill (for plan generation)
- **Consults**: PM agent, Architect agent
- **Reads**: Project plan, git status

## Error Handling

| Error                          | Recovery                                                      |
| ------------------------------ | ------------------------------------------------------------- |
| GitHub API unavailable         | Fall back to project plan only (no issue-level dependencies)  |
| Issue fetch fails              | Parse what's available, warn about incomplete dependency data |
| Dependency issue not found     | Mark as unresolved blocker, recommend user check issue number |
| Circular dependencies detected | Flag error, show dependency cycle, recommend user fix         |

**Graceful degradation**: If GitHub issues can't be fetched, the skill still works using the project plan's workstream dependencies, just without fine-grained issue-level blocking.
