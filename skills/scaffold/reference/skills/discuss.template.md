````skill
---
name: discuss
description: Facilitates multi-agent discussions on decisions or topics - routes to stakeholders, synthesizes perspectives. Returns consensus or disagreements with recommendations.
allowed-tools: Read, Grep, Glob
---

# Discuss Skill

Facilitate structured discussions for decisions requiring multiple perspectives.

## Triggers

| Trigger | Action |
|---------|--------|
| `discuss "should we use X?"` | Start discussion |
| "let's discuss this" | Discuss topic from context |
| "what do agents think about..." | Multi-perspective analysis |

## When to Use

- Architectural decisions with trade-offs
- Technology choices
- Design pattern selection
- Approach disagreements
- Risk assessment

## Workflow

### Phase 1: Frame

1. **Identify the decision**: What exactly needs to be decided?
2. **List stakeholders**: Which agents should weigh in?
3. **Set context**: What constraints or goals apply?

### Phase 2: Gather Perspectives

For each relevant agent, consider their viewpoint:

| Agent | Perspective Focus |
|-------|-------------------|
| Architect | System design, scalability, maintainability |
| Frontend | User experience, performance, accessibility |
| Backend | Data, security, reliability |
| Testing | Testability, coverage, maintenance |
| Auditor | Quality, security, compliance |

**For each stakeholder:**
```markdown
### [Agent Name] Perspective

**Position**: [Support / Oppose / Neutral]

**Key Points**:
- [Point 1]
- [Point 2]

**Concerns**:
- [Concern 1]

**Conditions for Support**:
- [If X, then support]
```

### Phase 3: Synthesize

1. **Identify consensus**: Where do agents agree?
2. **Identify conflicts**: Where do they disagree?
3. **Rank options**: Based on weighted criteria

```markdown
## Discussion Summary

### Topic
[The decision being made]

### Options Considered
1. **Option A**: [Description]
2. **Option B**: [Description]

### Consensus Points
- ✅ [All agree on X]
- ✅ [All agree on Y]

### Disagreements
- ⚠️ [Agent1 prefers A because..., Agent2 prefers B because...]

### Recommendation
**Option [X]** — [Rationale]

### Trade-offs Accepted
- [What we give up with this choice]

### Follow-up Items
- [ ] [Action needed]
```

### Phase 4: Decision

| Outcome | Action |
|---------|--------|
| Clear consensus | Document decision, proceed |
| Minor disagreement | Note trade-offs, recommend |
| Major disagreement | Escalate to human with options |

## Output Format

```markdown
## Discussion: [Topic]

**Date**: YYYY-MM-DD
**Participants**: [Agent list]
**Decision**: [Outcome or "Needs human input"]

### Summary
[One paragraph synthesis]

### Perspectives
[Stakeholder views]

### Recommendation
[Final recommendation with reasoning]
```
````
