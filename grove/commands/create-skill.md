---
name: grove:create-skill
description: Create new skills, agents, or commands for the grove plugin
argument-hint: "[skill|agent|command] [name]"
---

# Skill Creator

Create a new component for the grove plugin.

## Input

<input> #$ARGUMENTS </input>

## Instructions

**First, ask the user where to create the component:**

- **Project** - Available only in this project
  - Skills: `skills/<name>/SKILL.md`
  - Agents: `agents/<category>/<name>.md`
  - Commands: `.claude/commands/<name>.md`
- **Global** - Available in all projects
  - Skills: `~/.claude/skills/<name>/SKILL.md`
  - Agents: `~/.claude/agents/<category>/<name>.md`
  - Commands: `~/.claude/commands/<name>.md`

Use the AskUserQuestion tool to prompt for this choice before proceeding.

If no arguments provided, also ask the user what they want to create:
- **skill** - Domain knowledge loaded on-demand
- **agent** - Specialized persona for specific tasks
- **command** - Slash-invoked workflow

Then load the create-skill skill and follow its guidance to create the requested component.
