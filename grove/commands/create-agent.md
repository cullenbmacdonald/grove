---
name: grove:create-agent
description: Create a new specialized agent following Anthropic's best practices
argument-hint: "[agent purpose and requirements]"
---

# Create a Specialized Agent

Create a new agent in `agents/<category>/` for the requested purpose.

## Goal

#$ARGUMENTS

## Instructions

Load the `agent-creator` skill for detailed guidance on agent design, patterns, and best practices drawn from Anthropic's research.

## Quick Reference

### Required YAML Frontmatter

```yaml
---
name: agent-name
description: "Use this agent when [conditions].\n\n<example>\nContext: [scenario]\nuser: \"[request]\"\nassistant: \"I'll use agent-name to [action]\"\n<commentary>\n[Why this agent is appropriate]\n</commentary>\n</example>"
model: inherit
---
```

### Agent Structure

```markdown
You are [role description].

Your approach:

1. **First Area**: Description
   - Sub-point

2. **Second Area**: Description
   - Sub-point

When working, provide:
- Clear analysis
- Specific recommendations
- Examples when helpful
```

### Key Principles

1. **Start simple** - One clear purpose, minimal complexity
2. **Design tools carefully** - Each tool should be non-overlapping and well-documented
3. **Include verification** - Agents should check their own work
4. **Scope tightly** - A focused agent outperforms a general one

### Agent Categories

- `research/` - Information gathering and analysis
- `review/` - Code review and quality checks
- `workflow/` - Process automation

### Creating the File

1. Create at `agents/<category>/<name>.md`
2. Start with YAML frontmatter including `<example>` tags
3. Define a clear role and structured approach
4. Use `model: inherit` (or `haiku` for simple tasks)

For detailed patterns, design principles, and Anthropic's agent-building guidance, see the `agent-creator` skill.
