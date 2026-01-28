# Grove Plugin

A minimal Claude Code plugin that grows organically. Add capabilities only when you need them.

## Philosophy

- Start minimal, add when you feel the pain
- Language-agnostic by default
- Document solutions as you go

## Adding Components

### Skills

Create `skills/<skill-name>/SKILL.md`:

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

Create `agents/<category>/<agent-name>.md`:

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

Create `commands/<command-name>.md`:

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
