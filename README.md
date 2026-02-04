# AI-First Scaffold

Set up AI-first development infrastructure for any project. Works with Claude Code, GitHub Copilot CLI, and VS Code Copilot.

## Features

- 🤖 **8 specialized agents** — Select the ones you need (program-manager, architect, frontend, backend, testing, design, auditor, ai-prompts)
- 📋 **5 workflow skills** — status, implement, prepare-work, discuss, review-artifact
- 📊 **Progress tracking** — PROGRESS.md for sprint and work unit management
- 🔧 **Stack-agnostic** — Generates configs for any tech stack (Node.js, Python, Go, Rust, etc.)
- 💬 **Interview-based** — AI asks about your project, then generates tailored files

## Installation

### Claude Code

```bash
# Option 1: Use as plugin
claude --plugin-dir /path/to/ai-first-scaffold

# Option 2: Install as personal skill
git clone https://github.com/AbdouMoumen/ai-first-scaffold ~/.claude/skills/ai-first-scaffold
```

### GitHub Copilot CLI

```bash
git clone https://github.com/AbdouMoumen/ai-first-scaffold ~/.copilot/skills/ai-first-scaffold
```

### VS Code Copilot

Clone to `.github/skills/` in your repository, or use the marketplace (coming soon).

## Usage

### Via Slash Command (Claude Code)

```
/ai-first:scaffold my-project
```

### Via Natural Language (Any Platform)

```
> Set up AI-first development for this project
```

### What Happens

1. **Interview**: AI asks about your project
   - Project name and description
   - Tech stack (describe naturally)
   - Domain terminology (custom terms)
   - Hard constraints (rules to enforce)
   - Team structure (determines agent selection)

2. **Detection**: AI checks for existing files
   - Won't overwrite without asking
   - Adapts to your platform

3. **Generation**: AI creates tailored files
   - AGENTS.md with your terminology and constraints
   - Agents selected for your team size
   - Skills adapted to your stack's commands

4. **Summary**: AI reports what was created

## What Gets Generated

```
your-project/
├── AGENTS.md                    # Main AI entrypoint
├── PROGRESS.md                  # Work tracking
├── agents/
│   ├── program-manager.md       # Always
│   ├── architect.md             # Always
│   ├── testing.md               # Always
│   ├── auditor.md               # Always
│   ├── frontend.md              # If has UI
│   ├── backend.md               # If has backend
│   ├── design.md                # If has UI design needs
│   └── ai-prompts.md            # If AI-focused
└── .claude/skills/              # Or .github/skills/
    ├── status/SKILL.md
    ├── implement/SKILL.md
    ├── prepare-work/SKILL.md
    ├── discuss/SKILL.md
    └── review-artifact/SKILL.md
```

## Agent Selection

| Team Size | Agents Included |
|-----------|-----------------|
| Solo developer | program-manager, architect, testing, auditor |
| Small team (2-4) | + frontend OR backend |
| Full team (5+) | All 8 agents |
| Has UI | + design |
| AI-focused | + ai-prompts |

## Skills Overview

| Skill | Purpose |
|-------|---------|
| **status** | Check project state, detect inconsistencies |
| **implement** | Execute work with tests, review, commit |
| **prepare-work** | Transform issues into implementation plans |
| **discuss** | Multi-agent discussions for decisions |
| **review-artifact** | Quality review with severity ranking |

## Validation

After setup, validate your infrastructure:

```
/ai-first:validate
```

This checks:
- AGENTS.md has required sections
- PROGRESS.md has valid frontmatter
- Agents have proper structure
- Skills are correctly configured

## Customization

### Terminology

Add domain-specific terms in AGENTS.md:

```markdown
## Terminology

| Term | Meaning | Don't Use |
|------|---------|-----------|
| **Customer** | End user of the system | user, client |
| **Order** | Purchase transaction | transaction, purchase |
```

### Constraints

Add project rules in AGENTS.md:

```markdown
## Architecture Constraints

1. **Offline-First**: All data operations must work offline
2. **No Direct DB Access**: Always use the service layer
3. **Audit Everything**: All mutations must be logged
```

### Stack Commands

The pre-commit commands are auto-detected from your stack:

- **Node.js**: `npm run lint && npm run test && npm run build`
- **Python**: `ruff check . && pytest && mypy .`
- **Go**: `go fmt ./... && go vet ./... && go test ./...`
- **Rust**: `cargo fmt --check && cargo clippy && cargo test`

## Plugin Structure

```
ai-first-scaffold/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── skills/
│   └── scaffold/
│       ├── SKILL.md             # Main scaffolding skill
│       └── reference/           # Templates
│           ├── AGENTS.template.md
│           ├── PROGRESS.template.md
│           ├── agents/          # 8 agent templates
│           └── skills/          # 5 skill templates
├── commands/
│   ├── scaffold.md              # /ai-first:scaffold
│   └── validate.md              # /ai-first:validate
├── agents/
│   └── scaffold-agent.md        # Custom agent for setup
├── README.md
└── LICENSE
```

## Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## License

MIT License — see [LICENSE](LICENSE).

---

Built with learnings from [Khatmah](https://github.com/AbdouMoumen/khatmah), a Quran listening PWA with comprehensive AI-first development infrastructure.
