````skill
---
name: scaffold
description: Set up AI-first development infrastructure - interviews about project, stack, and team, then generates agents, skills, and workflows. Use when setting up a new project or adding AI-first patterns to existing code.
allowed-tools: Bash, Read, Write, Edit, Grep, Glob
---

# AI-First Project Scaffold

Generate complete AI-first development infrastructure tailored to any project.

## When to Use

| User Says | Action |
|-----------|--------|
| "Set up AI-first development" | Run full interview + generation |
| "Scaffold agents and skills" | Run full interview + generation |
| "Initialize AI-first project" | Run full interview + generation |
| "Add AI workflows to this project" | Run full interview + generation |

## Constraints (Read First)

- **Interview first**: Always gather requirements before generating
- **Never assume stack**: Let user describe their tech freely
- **Preserve existing**: Check for existing files, ask before overwriting
- **Platform-agnostic**: Generate for user's AI tool (Claude Code, Copilot CLI, etc.)
- **No domain bias**: Templates are generic, user provides domain terms

---

## Phase 1: Interview

**Goal**: Understand the project through natural conversation.

### Core Questions (Always Ask)

Ask these in a conversational flow, not as a rigid form:

#### 1. Project Identity
```
What's the project name and a one-sentence description?
```

Store: `project.name`, `project.description`

#### 2. Tech Stack
```
What technologies are you using? (Just describe naturally - I'll adapt)
```

Parse for:
- Language(s): JavaScript/TypeScript, Python, Go, Rust, etc.
- Frameworks: Next.js, FastAPI, Django, Express, etc.
- Database: PostgreSQL, MongoDB, Supabase, etc.
- Testing: Vitest, pytest, Jest, etc.
- Linting: ESLint, Ruff, golint, etc.

Store: `stack.language`, `stack.framework`, `stack.database`, `stack.testing`, `stack.linting`

#### 3. Domain Terminology (Optional)
```
Does this project have domain-specific terms I should use?
(Skip if using standard software terms)
```

Examples to prompt (only if they have a specific domain):
- Medical: "patient" not "user", "encounter" not "visit"
- Finance: "transaction", "settlement", "clearing"
- E-commerce: "order", "SKU", "cart"

If user says "no" or "standard terms", skip this section in generated files.

If user provides terms, store as table:
```
| Term | Meaning | Don't Use |
|------|---------|-----------|
```

Store: `domain.terminology[]` (empty array if skipped)

#### 4. Hard Constraints
```
Any absolute rules for this project? Things like "never do X" or "always use Y".
```

Examples to prompt:
- Security constraints (never log PII)
- Architectural rules (always use service layer)
- Compliance requirements (audit all changes)

Store: `domain.constraints[]`

#### 5. Team Structure
```
Working solo or with a team? What roles?
```

| Response | Agent Selection |
|----------|-----------------|
| Solo developer | Core 4: program-manager, architect, testing, auditor |
| Small team (2-4) | Core 4 + domain-specific (frontend/backend) |
| Full team (5+) | All 8 agents |

Store: `team.size`, `team.roles[]`

### Adaptive Questions (Based on Responses)

| If User Mentions | Ask |
|------------------|-----|
| Web/mobile UI | "Do you need visual design guidance?" → Include design agent |
| Backend/API | "What's your API style? REST, GraphQL, RPC?" |
| Existing codebase | "Should I analyze existing patterns first?" |
| CI/CD | "What CI system? GitHub Actions, GitLab CI, etc.?" |

### Interview Summary

Before proceeding, confirm understanding:

```markdown
## Project Summary

**Name**: {{project.name}}
**Description**: {{project.description}}

**Stack**: {{stack summary}}
**Team**: {{team.size}} - {{team.roles}}

**Terminology**: {{terminology table or "Standard terms (none specified)"}}

**Constraints**: {{constraints list or "None specified"}}

**Agents to Generate**: {{agent list}}

Does this look right? (yes/adjust)
```

---

## Phase 2: Detection

**Goal**: Understand existing project structure.

### 2.1: Check Platform

Detect which AI tool the user is using:

```bash
# Check for existing AI configurations
ls -la .claude/ 2>/dev/null
ls -la .github/copilot-instructions.md 2>/dev/null
ls -la .github/agents/ 2>/dev/null
ls -la .cursor/ 2>/dev/null
```

| Found | Platform | Skills Location |
|-------|----------|-----------------|
| `.claude/` | Claude Code | `.claude/skills/` |
| `.github/agents/` | Copilot CLI | `.github/skills/` |
| Neither | Ask user | `.claude/skills/` (default) |

### 2.2: Check Existing Files

```bash
# Check for files we might overwrite
ls -la AGENTS.md 2>/dev/null
ls -la PROGRESS.md 2>/dev/null
ls -la agents/ 2>/dev/null
```

If any exist:
```
Found existing AI infrastructure:
- AGENTS.md (exists)
- agents/ (3 files)

Options:
1. Merge with existing (add new, keep old)
2. Replace all (backup first)
3. Cancel and review manually

Your choice?
```

### 2.3: Detect Project Type

```bash
# Detect build system
ls package.json 2>/dev/null && echo "NODE"
ls pyproject.toml setup.py requirements.txt 2>/dev/null && echo "PYTHON"
ls go.mod 2>/dev/null && echo "GO"
ls Cargo.toml 2>/dev/null && echo "RUST"
ls Makefile 2>/dev/null && echo "MAKE"
```

Use detected type to:
- Set default commands in AGENTS.md
- Pre-fill testing/linting commands
- Suggest appropriate CI workflow

---

## Phase 3: Generation

**Goal**: Create all files from templates.

### 3.1: Read Templates

Templates are in `reference/` directory relative to this skill:

```
reference/
├── AGENTS.template.md
├── PROGRESS.template.md
├── agents/
│   ├── program-manager.template.md
│   ├── architect.template.md
│   ├── frontend.template.md
│   ├── backend.template.md
│   ├── testing.template.md
│   ├── design.template.md
│   ├── auditor.template.md
│   └── ai-prompts.template.md
└── skills/
    ├── status.template.md
    ├── implement.template.md
    ├── prepare-work.template.md
    ├── discuss.template.md
    └── review-artifact.template.md
```

### 3.2: Variable Substitution

Replace these placeholders in templates:

| Placeholder | Source | Example |
|-------------|--------|---------|
| `{{PROJECT_NAME}}` | interview.project.name | "MyApp" |
| `{{PROJECT_DESCRIPTION}}` | interview.project.description | "A task management app" |
| `{{TERMINOLOGY_TABLE}}` | interview.domain.terminology | Markdown table (or omit section if empty) |
| `{{DOMAIN_CONSTRAINTS}}` | interview.domain.constraints | Bullet list |
| `{{TECH_STACK}}` | interview.stack | "Next.js, TypeScript, Supabase" |
| `{{COMMANDS_TABLE}}` | Derived from stack | npm/yarn/pnpm commands |
| `{{LINT_COMMAND}}` | Derived from stack | "npm run lint" |
| `{{TEST_COMMAND}}` | Derived from stack | "npm run test" |
| `{{BUILD_COMMAND}}` | Derived from stack | "npm run build" |
| `{{PRE_COMMIT_COMMANDS}}` | Derived from stack | Validation command chain |

**Conditional sections:**
- If `domain.terminology` is empty → omit Terminology section entirely from AGENTS.md
- Agent files reference AGENTS.md for context (no duplication)

### 3.3: Generate Files

Create files based on interview responses:

**Always generate:**
- `AGENTS.md` — Main AI entrypoint
- `PROGRESS.md` — Empty progress tracker
- `docs/` — Documentation structure (if doesn't exist)

**Generate selected agents:**

Based on `team.size` and project type:

| Team Size | Agents |
|-----------|--------|
| Solo | program-manager, architect, testing, auditor |
| Small (2-4) | + frontend OR backend (based on stack) |
| Full (5+) | All 8 agents |
| Has UI | + design |
| Has AI focus | + ai-prompts |

**Generate core skills:**

Always generate these 5 skills:
1. `status` — Project state validation
2. `implement` — Execute plans with review
3. `prepare-work` — Transform issues into plans
4. `discuss` — Multi-perspective discussions
5. `review-artifact` — Quality review process

### 3.4: Stack-Specific Generation

Based on detected/stated stack, generate:

**Node.js/TypeScript:**
```
Pre-commit: npm run lint && npm run test -- --run && npm run build
```

**Python:**
```
Pre-commit: ruff check . && pytest && mypy .
```

**Go:**
```
Pre-commit: go fmt ./... && go vet ./... && go test ./...
```

**Rust:**
```
Pre-commit: cargo fmt --check && cargo clippy && cargo test
```

---

## Phase 4: Verification

**Goal**: Confirm successful generation.

### 4.1: List Created Files

```bash
echo "=== Files Created ==="
find . -name "AGENTS.md" -o -name "PROGRESS.md" | head -20
find agents/ -name "*.md" 2>/dev/null | head -20
find .claude/skills/ -name "SKILL.md" 2>/dev/null | head -20
find .github/skills/ -name "SKILL.md" 2>/dev/null | head -20
```

### 4.2: Summary Report

```markdown
## 🎉 AI-First Infrastructure Created

**Location**: {{skills_location}}

### Files Created

| File | Purpose |
|------|---------|
| `AGENTS.md` | Main AI entrypoint with project rules |
| `PROGRESS.md` | Sprint/work tracking |
| `agents/` | {{agent_count}} specialized agents |
| `{{skills_location}}` | {{skill_count}} workflow skills |

### Agents Generated

{{for each agent}}
- **{{agent.name}}** — {{agent.role}}
{{end}}

### Skills Generated

{{for each skill}}
- **{{skill.name}}** — {{skill.description}}
{{end}}

### Next Steps

1. **Review AGENTS.md** — Customize terminology and constraints
2. **Test a skill** — Try `/status` to verify setup
3. **Read agents/** — Familiarize yourself with each agent's role
4. **Start working** — Use `/implement` for your first task

### Quick Commands

| Action | Command |
|--------|---------|
| Check status | `/status` |
| Find next work | `/next` |
| Start implementation | `/implement #issue` or `/implement` |
| Get help | Ask about any file |
```

---

## Output Format

### For Interactive Use

Use the markdown format shown in Phase 4.2.

### For Programmatic Use

Return JSON:

```json
{
  "project": {
    "name": "{{name}}",
    "description": "{{description}}"
  },
  "files_created": [
    "AGENTS.md",
    "PROGRESS.md",
    "agents/program-manager.md",
    ...
  ],
  "agents": ["program-manager", "architect", ...],
  "skills": ["status", "implement", ...],
  "skills_location": ".claude/skills/",
  "platform": "claude-code"
}
```

---

## Error Handling

| Error | Recovery |
|-------|----------|
| Template not found | Report missing file, generate minimal version |
| File exists | Ask before overwriting |
| Unknown stack | Use generic commands, note in output |
| Interview cancelled | Save partial state, offer to resume |

---

## Configuration

Stored in generated `AGENTS.md`:

| Setting | Default | Customizable |
|---------|---------|--------------|
| Skills location | `.claude/skills/` | Yes |
| Agent selection | Based on team | Yes |
| Pre-commit commands | Based on stack | Yes |
| Terminology | User-provided | Yes |

---

## Related

- [reference/AGENTS.template.md](reference/AGENTS.template.md) — Main template
- [reference/agents/](reference/agents/) — Agent templates
- [reference/skills/](reference/skills/) — Skill templates
````
