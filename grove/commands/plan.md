---
name: grove:plan
description: Create a comprehensive plan using parallel specialist agents
argument-hint: "[task or feature to plan]"
---

# Plan

Create a comprehensive plan by orchestrating specialist agents in parallel.

## Input

<input> #$ARGUMENTS </input>

## Instructions

Load the `plan` skill for detailed guidance on running parallel planning agents and synthesizing a unified plan document.

## Quick Reference

### How It Works

This command discovers specialist agents across multiple disciplines and runs them in parallel to produce a comprehensive plan:

```
Input → [Coder Agent]    ─┐
      → [Designer Agent]  ─┼→ Synthesize into unified plan
      → [BA Agent]        ─┘
```

### Agent Discovery

Check for planning-capable agents in these directories:

| Directory | Purpose |
|-----------|---------|
| `.claude/agents/coder/` | Engineering/implementation planning |
| `.claude/agents/design/` | UX/UI and design planning |
| `.claude/agents/business/` | Business analysis, product strategy |
| `.claude/agents/research/` | Research and discovery |

Any `.md` agent files found in these directories will be launched in parallel.

### Running a Plan

1. Check for available agents across all directories
2. If agents exist, launch them all in parallel using the Task tool
3. Each agent produces a plan from its discipline's perspective
4. Synthesize all perspectives into a single unified plan document
5. Present the plan with clear sections, dependencies, and priorities

### If No Agents Exist

If no agent directories contain planning agents, inform the user:

```
No planning agents found.

Create agents that match your team's disciplines:
  /grove:create-agent coder agent for backend architecture planning
  /grove:create-agent designer agent for UX/UI planning
  /grove:create-agent business analyst for requirements and impact analysis

Agents should be placed in .claude/agents/<category>/ and follow the agent template.
See the agent-creator skill for guidance.
```

### Arguments

- If arguments provided: Plan for the specified task/feature
- If no arguments: Ask the user what they want to plan

For detailed patterns and guidance, see the `plan` skill.
