---
name: create-skill
description: This skill should be used when creating new skills, agents, or commands for the grove plugin. It provides templates and guidance for each component type. Triggers on "create skill", "new agent", "add command", "extend grove".
---

# Skill Creator

Guide for creating new grove plugin components.

## Skills

Skills provide domain knowledge loaded on-demand. Create `skills/<name>/SKILL.md`.

### Template

```markdown
---
name: my-skill
description: This skill should be used when [specific trigger conditions]. It provides [what it does]. Triggers on "keyword1", "keyword2", "keyword3".
allowed-tools: Read, Grep, Glob
---

# My Skill

## Overview

Brief description of what this skill does.

## Quick Start

Minimal example of using the skill.

## Detailed Usage

More comprehensive instructions.

## Success Criteria

How to know the skill was applied correctly.
```

### Rules

1. **Name**: Lowercase, hyphens only, must match directory name
2. **Description**: Third person ("This skill should be used when..."), include trigger keywords
3. **Keep lean**: Under 500 lines; use `references/` for detailed docs
4. **Progressive disclosure**: Core instructions in SKILL.md, details in references/

### Optional Directories

- `scripts/` - Executable code for deterministic tasks
- `references/` - Documentation loaded as needed
- `assets/` - Files used in output (templates, images)

## Agents

Agents are specialized personas invoked via `Task agent-name(arguments)`. Create `agents/<category>/<name>.md`.

### Categories

- `research/` - Information gathering and analysis
- `review/` - Code review and quality checks
- `workflow/` - Process automation

### Template

```markdown
---
name: my-agent
description: "Use this agent when [conditions].\n\n<example>\nContext: [scenario]\nuser: \"[request]\"\nassistant: \"I'll use my-agent to [action]\"\n<commentary>\n[Why this agent is appropriate]\n</commentary>\n</example>"
model: inherit
---

You are [role description].

Your approach:

1. **First Area**: Description
   - Sub-point
   - Sub-point

2. **Second Area**: Description
   - Sub-point

When working, provide:
- Clear analysis
- Specific recommendations
- Examples when helpful
```

### Rules

1. **Name**: Lowercase with hyphens
2. **Description**: Include `<example>` tags showing when to use
3. **Model**: Use `inherit` to match conversation model, or specify `haiku` for simple tasks

## Commands

Commands are slash-invoked workflows. Create `commands/<name>.md`.

### Template

```markdown
---
name: my-command
description: What this command does
argument-hint: "[required input] [optional: flag]"
---

# My Command

## Description

What this command does and when to use it.

## Input

<input> #$ARGUMENTS </input>

**If empty**, ask the user for the required input.

## Steps

### 1. First Step

Description of what to do.

### 2. Second Step

More instructions.

- Task agent-name(arguments) - Call an agent if needed

## Output

How to format the output.
```

### Rules

1. **Name**: Lowercase, becomes `/name`
2. **$ARGUMENTS**: User input after the command
3. **Task syntax**: `Task agent-name(arguments)` to invoke agents

## Version Bump

After creating any component, bump version in `.claude-plugin/plugin.json`:

```json
{
  "name": "grove",
  "version": "0.2.0",  // Bump this
  "description": "A minimal, incrementally-grown Claude Code plugin"
}
```

## Common Patterns

### Language-Agnostic Review Agent

```markdown
---
name: code-reviewer
description: "Use this agent for general code review across any language."
model: inherit
---

You are a code reviewer focused on clarity, correctness, and simplicity.

Review for:
1. Logic errors
2. Edge cases
3. Unnecessary complexity
4. Missing error handling
```

### Research Agent

```markdown
---
name: codebase-explorer
description: "Use this agent to understand unfamiliar codebases."
model: inherit
---

You are a codebase analyst.

Your approach:
1. Find entry points
2. Trace data flow
3. Identify patterns
4. Summarize architecture
```

### Workflow Command

```markdown
---
name: review
description: Run code review on recent changes
---

# Review Command

<scope> #$ARGUMENTS </scope>

## Steps

1. Get changed files: `git diff --name-only`
2. Task code-reviewer("Review these changes: [files]")
3. Summarize findings
```
