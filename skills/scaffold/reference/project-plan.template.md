# {{PROJECT_NAME}} Project Plan

**Version**: 1.0  
**Created**: {{TODAY_DATE}}  
**Owner**: Program Manager Agent

---

## Executive Summary

{{PROJECT_DESCRIPTION}}

> **Agentic Workflow**: This plan uses goal-based sprints. When triggered, AI agents decompose goals into work units, implement them, and return completed work for review. Humans trigger work units, provide credentials, and approve PRs.

---

## Phase Overview

| Phase | Sprints | Goal | Key Deliverables |
|-------|---------|------|------------------|
| **Phase 1: Foundation** | Sprint 1 | Core infrastructure | Project setup, core architecture |
| **Phase 2: Core Features** | Sprint 2-3 | MVP functionality | Primary features |
| **Phase 3: Polish** | Sprint 4 | Production ready | Testing, performance, launch |

---

## How Sprints Work (Agentic Model)

### For Each Sprint:

1. **Human triggers**: Work unit from a workstream
2. **AI executes**: Completes work unit (explore → plan → code → verify)
3. **AI returns**: Completed work ready for review
4. **Human reviews**: Approves PR or requests changes
5. **Repeat**: Human triggers next work unit

### Human Checkpoints

| Checkpoint | When | What Human Does |
|------------|------|-----------------|
| Work unit trigger | Before each unit | "Start [work unit]" with any needed context |
| Credential provision | When requested | API keys, service tokens, etc. |
| Design approval | After design artifacts | Approve visual direction |
| PR review | Before merge | Review code, approve/request changes |
| Sprint review | End of sprint | Accept deliverables, provide feedback |

---

## Sprint 1: Foundation

### Sprint Goal

> **Establish project infrastructure**: working dev environment, core architecture, and basic functionality.

### Success Criteria

- [ ] Development environment running (`{{DEV_COMMAND}}`)
- [ ] Core data models defined
- [ ] Basic UI scaffold in place
- [ ] CI/CD pipeline configured
- [ ] Project deploys successfully

### Workstreams

| # | Workstream | Work Units | Dependencies |
|---|------------|------------|--------------|
| 1 | Project Setup | Environment, tooling, CI | None |
| 2 | Core Architecture | Data models, API structure | W1 |
| 3 | Basic UI | Layout, navigation, components | W1 |

---

## Sprint 2: Core Features

### Sprint Goal

> **Implement primary functionality**: the main features that deliver user value.

### Success Criteria

- [ ] Primary user flow works end-to-end
- [ ] Core features implemented
- [ ] Basic test coverage
- [ ] Documentation started

### Workstreams

| # | Workstream | Work Units | Dependencies |
|---|------------|------------|--------------|
| 1 | Feature A | [Derived from project description] | Sprint 1 |
| 2 | Feature B | [Derived from project description] | Sprint 1 |
| 3 | Integration | Connect features, test flows | W1, W2 |

---

## Sprint 3: Enhancement

### Sprint Goal

> **Expand functionality**: secondary features and improved UX.

### Success Criteria

- [ ] Secondary features implemented
- [ ] Error handling comprehensive
- [ ] Performance acceptable
- [ ] User feedback incorporated

### Workstreams

| # | Workstream | Work Units | Dependencies |
|---|------------|------------|--------------|
| 1 | Enhancement A | [Based on project needs] | Sprint 2 |
| 2 | Enhancement B | [Based on project needs] | Sprint 2 |
| 3 | Polish | UX improvements, edge cases | W1, W2 |

---

## Sprint 4: Launch

### Sprint Goal

> **Production readiness**: testing, performance, security, and deployment.

### Success Criteria

- [ ] All tests passing
- [ ] Performance optimized
- [ ] Security reviewed
- [ ] Documentation complete
- [ ] Production deployed

### Workstreams

| # | Workstream | Work Units | Dependencies |
|---|------------|------------|--------------|
| 1 | Testing | Unit, integration, E2E | Sprint 3 |
| 2 | Performance | Optimization, monitoring | Sprint 3 |
| 3 | Launch | Security, docs, deploy | W1, W2 |

---

## Human Input Required

| Item | Sprint | Needed For |
|------|--------|------------|
| [Service] API keys | Sprint 1 | Backend integration |
| Design approval | Sprint 1 | UI implementation |
| Domain/hosting | Sprint 4 | Production deployment |

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Scope creep | Medium | High | Strict MVP definition |
| API limitations | Low | Medium | Early integration testing |
| Timeline slip | Medium | Medium | Buffer in Sprint 4 |

---

## Notes

- This plan will be refined as the project progresses
- Workstreams within a sprint can run in parallel if they don't conflict
- Use `next-work-item` skill to identify what's ready to start
- Update this plan and PROGRESS.md together when sprints transition
