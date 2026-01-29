---
name: grove:review
description: Run code review agents in parallel following Anthropic's patterns
argument-hint: "[files or directories to review]"
---

# Code Review

Run review agents in parallel to analyze code from multiple perspectives.

## Input

<input> #$ARGUMENTS </input>

## Instructions

Load the `review` skill for detailed guidance on running parallel review agents and understanding the review process.

## Quick Reference

### How It Works

This command orchestrates review agents from `.claude/agents/review/` using Anthropic's **parallelization pattern**:

```
Input → [Agent A] ─┐
      → [Agent B] ─┼→ Synthesize findings
      → [Agent C] ─┘
```

Each review agent examines code from a focused perspective and runs in parallel for speed.

### Running Reviews

1. Check for available review agents in `.claude/agents/review/`
2. If agents exist, launch them in parallel using the Task tool
3. Collect and synthesize findings from all agents
4. Present unified results with prioritized recommendations

### If No Agents Exist

If the `.claude/agents/review/` directory is empty or doesn't exist, inform the user:

```
No review agents found in .claude/agents/review/.

To create review agents, use:
  /create-agent [agent purpose, e.g., "security reviewer focused on OWASP Top 10"]

Review agents should be placed in .claude/agents/review/ and follow the agent template.
See the agent-creator skill for guidance.
```

### Arguments

- If arguments provided: Review the specified files/directories
- If no arguments: Review staged changes (`git diff --cached`) or prompt user for scope

For detailed patterns and guidance, see the `review` skill.
