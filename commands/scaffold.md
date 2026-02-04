---
description: Launch AI-first project scaffolding to set up agents, skills, and workflows
---

# Scaffold Command

Set up AI-first development infrastructure for this project.

## What to Do

If `$ARGUMENTS` is provided, use it as the project name. Otherwise, ask interactively.

Invoke the scaffold skill to:

1. **Interview the user** about their project:
   - Project name and description
   - Tech stack (let them describe naturally)
   - Domain terminology (custom terms to use)
   - Hard constraints (rules that must never be broken)
   - Team structure (solo, small team, full team)

2. **Detect existing structure**:
   - Check for `.claude/`, `.github/agents/`, existing AGENTS.md
   - Identify project type from package.json, pyproject.toml, etc.
   - Ask before overwriting anything

3. **Generate files**:
   - AGENTS.md — Main AI entrypoint
   - PROGRESS.md — Work tracking
   - agents/*.md — Specialized agents (based on team size)
   - skills/ — Core workflow skills

4. **Summarize**:
   - List all files created
   - Explain what each does
   - Provide next steps

## Example Usage

```
/ai-first:scaffold my-app
```

Or without arguments to start interactively:

```
/ai-first:scaffold
```
