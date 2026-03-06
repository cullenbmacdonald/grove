---
name: plan
description: This skill provides guidance for creating comprehensive plans using parallel specialist agents across engineering, design, and business disciplines. It orchestrates agents from multiple .claude/agents/ categories to produce a unified plan document. Triggers on "plan feature", "create plan", "comprehensive plan", "compound plan".
---

# Plan

Expert guidance for creating comprehensive, multi-discipline plans by orchestrating specialist agents in parallel.

## Core Concept

Good plans require multiple perspectives: engineering feasibility, user experience, business impact, and more. Rather than a single-discipline plan, this skill orchestrates **parallel specialist agents** that each contribute their expertise.

From Anthropic's research:

> **Parallelization - Sectioning:** Break a task into independent subtasks that run simultaneously.
> Best for: Speed improvements or gaining confidence through multiple perspectives.

## Agent Discovery

Planning agents can live in any of these directories. Discover what's available:

```
Glob: .claude/agents/coder/*.md
Glob: .claude/agents/design/*.md
Glob: .claude/agents/business/*.md
Glob: .claude/agents/research/*.md
```

All directories are optional. Use whatever agents exist. If additional category directories exist in `.claude/agents/`, check those too — the system is extensible.

## Running a Plan

### Step 1: Discover Available Agents

Glob across all agent directories to find what's available. Any `.md` file in a recognized category directory is a planning agent.

### Step 2: Handle No Agents Case

If no agents are found anywhere, inform the user clearly:

```markdown
No planning agents found in `.claude/agents/`.

Create agents that match your team's disciplines:
  /grove:create-agent coder agent for [your stack] architecture planning
  /grove:create-agent designer agent for UX/UI and interaction planning
  /grove:create-agent business analyst for requirements and impact analysis
  /grove:create-agent researcher for competitive analysis and discovery

Place agents in `.claude/agents/<category>/` and follow the standard agent template.
See the `agent-creator` skill for detailed guidance.
```

Do NOT proceed with planning if no agents exist. The user must create agents first.

### Step 3: Clarify the Task

Ensure you have a clear understanding of what needs to be planned. If the input is vague:
- Ask clarifying questions about scope, constraints, and goals
- Identify stakeholders and success criteria

### Step 4: Launch Agents in Parallel

Use the Task tool to launch all discovered agents simultaneously. Each agent receives the same task description and produces a plan from its discipline's perspective.

For each agent, the prompt should include:
1. The task/feature to plan
2. A request to produce a structured plan from their discipline's perspective
3. Instructions to identify dependencies, risks, and open questions

**Key principle:** All Task calls must be in a single message to enable true parallel execution.

**Example** — If you discovered `backend-architect.md` in `coder/`, `ux-planner.md` in `design/`, and `product-analyst.md` in `business/`:

```
Task backend-architect("Plan the engineering approach for: [task]. Produce a structured plan covering architecture, implementation steps, technical risks, and effort estimates.")

Task ux-planner("Plan the design approach for: [task]. Produce a structured plan covering user flows, wireframe concepts, design decisions, and usability considerations.")

Task product-analyst("Analyze business requirements for: [task]. Produce a structured plan covering user stories, acceptance criteria, business impact, and success metrics.")
```

### Step 5: Synthesize into Unified Plan

After all agents complete, combine their outputs into a single comprehensive plan document:

```markdown
# Plan: [Task/Feature Name]

## Overview
[1-2 paragraph summary of what's being planned and why]

## Goals & Success Criteria
- [Goal 1 with measurable success criteria]
- [Goal 2 with measurable success criteria]

## Plan by Discipline

### [Agent 1 Category - e.g., Engineering]
[Synthesized plan from coder agents]

### [Agent 2 Category - e.g., Design]
[Synthesized plan from design agents]

### [Agent 3 Category - e.g., Business]
[Synthesized plan from business agents]

[Continue for each discipline that had agents...]

## Cross-Cutting Concerns
[Dependencies between disciplines, shared assumptions, integration points]

## Risks & Mitigations
| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| [Risk from any discipline] | High/Med/Low | High/Med/Low | [Strategy] |

## Open Questions
- [Unresolved questions from any agent, grouped by discipline]

## Suggested Phases
1. **Phase 1**: [What to do first, across all disciplines]
2. **Phase 2**: [Next steps]
3. **Phase 3**: [Later work]

## Dependencies
[Cross-discipline dependencies, external dependencies, sequencing constraints]
```

### Step 6: Save the Plan

Write the unified plan document to `docs/plans/` using the naming convention:

```
docs/plans/YYYY-MM-DD-<slug>.md
```

Where `<slug>` is a short kebab-case summary of the task (e.g., `add-user-auth`, `redesign-checkout-flow`).

1. Ensure the `docs/plans/` directory exists (create it if not)
2. Write the synthesized plan to the file
3. Tell the user the file path so they can share or reference it

## Agent Design for Planning

Planning agents should follow these principles:

### Discipline-Focused Perspective

Each agent provides expertise from one discipline:

```markdown
You are a [discipline] specialist planning agent.

Given a task or feature, produce a structured plan covering:
1. [Discipline-specific concern 1]
2. [Discipline-specific concern 2]
3. [Discipline-specific concern 3]

Include: effort estimates, risks, dependencies, and open questions.
```

### Example Agent Types

**Coder Agents** — Architecture, tech stack decisions, implementation phases, API design, data models, testing strategy, performance considerations

**Design Agents** — User flows, wireframes/concepts, interaction patterns, accessibility, responsive design, design system alignment

**Business Agents** — User stories, acceptance criteria, market analysis, success metrics, stakeholder impact, prioritization, go/no-go criteria

**Research Agents** — Competitive analysis, technology evaluation, user research findings, feasibility studies

### Clear Output Format

Each agent should produce consistent output:

```markdown
## [Discipline] Plan

### Approach
[High-level strategy]

### Steps
1. [Step with effort estimate]
2. [Step with effort estimate]

### Dependencies
- [What this discipline needs from others]

### Risks
- [Discipline-specific risks]

### Open Questions
- [Unresolved questions]
```

## Verification

Before presenting the final plan:

- [ ] Discovered all agents across all category directories via Glob
- [ ] Launched all discovered agents in parallel (single message, multiple Task calls)
- [ ] Each agent's plan is represented in the unified document
- [ ] Cross-discipline dependencies are identified
- [ ] Risks are consolidated and prioritized
- [ ] Open questions are surfaced clearly
- [ ] Phases/sequencing accounts for dependencies across disciplines
- [ ] Plan saved to `docs/plans/YYYY-MM-DD-<slug>.md`

## Success Criteria

A successful plan:

- [ ] Checked for agents across all `.claude/agents/` category directories
- [ ] If no agents: Clearly informed user and provided guidance
- [ ] If agents exist: Launched all agents in parallel
- [ ] Synthesized outputs into a single unified plan document
- [ ] Identified cross-cutting concerns and dependencies
- [ ] Provided actionable phases with clear sequencing
- [ ] Saved plan to `docs/plans/` with date-prefixed filename
