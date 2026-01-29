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

Load the create-skill skill and follow its guidance to create the requested component.

If no arguments provided, ask the user what they want to create:
- **skill** - Domain knowledge loaded on-demand
- **agent** - Specialized persona for specific tasks
- **command** - Slash-invoked workflow

Guide them through the creation process using the templates in the create-skill skill.
