# Grove Plugin

Scaffolding for creating Claude Code skills, commands, and agents. Grove helps you build components that work natively with Claude Code - once created, they don't need this plugin.

## Philosophy

- **Scaffolding, not a cage** - Components you create are standard Claude Code formats. Uninstall grove anytime and everything keeps working.
- **Teach patterns, not dependencies** - Grove's skills teach Anthropic's best practices for agents and commands. The knowledge transfers.
- **Start minimal, add when you feel the pain** - Create components as you need them, not upfront.

## What Grove Creates

Everything grove creates uses standard Claude Code conventions:

| Component | Location | Works without grove? |
|-----------|----------|---------------------|
| Commands | `.claude/commands/<name>.md` | ✓ Yes |
| Agents | `.claude/agents/<category>/<name>.md` | ✓ Yes |
| Skills | `.claude/skills/<name>/SKILL.md` | ✓ Yes |

## Component Formats

### Skills

Create `.claude/skills/<skill-name>/SKILL.md`:

```markdown
---
name: skill-name
description: This skill should be used when [trigger conditions]. It provides [capabilities]. Triggers on "keyword1", "keyword2".
---

# Skill Name

## Overview

Brief description.

## Usage

Instructions for using the skill.
```

**Rules:**
- Directory name must match `name` field
- Description must use third person ("This skill should be used when...")
- Include trigger keywords in description

### Agents

Create `.claude/agents/<category>/<agent-name>.md`:

```markdown
---
name: agent-name
description: "Use this agent when [conditions]. <example>Context: [scenario]. user: \"[request]\" assistant: \"I'll use agent-name to...\"</example>"
model: inherit
---

You are [role description].

Your approach:
1. First step
2. Second step
```

**Categories:** `research/`, `review/`, `workflow/`

### Commands

Create `.claude/commands/<command-name>.md`:

```markdown
---
name: command-name
description: What the command does
argument-hint: "[required] [optional]"
---

# Command Name

## Description

<input> #$ARGUMENTS </input>

## Steps

1. First step
2. Second step
```

## Version Bumping

After any change, bump version in `.claude-plugin/plugin.json`.

## Current Stack

TypeScript, Python, Go, Bash, infrastructure
