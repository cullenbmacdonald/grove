---
title: "feat: Create Grove Plugin Seed"
type: feat
date: 2026-01-26
---

# Create Grove Plugin Seed

## Overview

Create a minimal Claude Code plugin called "grove" that provides just enough structure to grow organically. Starting with only the plugin skeleton and a skill-creator skill, you'll add capabilities as you need them.

## Problem Statement

The compound_engineering plugin has 28 agents, 24 commands, and 15 skills - most specialized for Rails, Every.to's style guide, Figma, and iOS development. You work with TypeScript, Python, Go, and Bash for infrastructure. Rather than pruning an overgrown garden, you want to plant your own seed and grow what you actually need.

## Proposed Solution

Create an ultra-minimal plugin with:
1. `plugin.json` - Plugin manifest
2. `CLAUDE.md` - Your development guidelines
3. `skill-creator` skill - Meta-skill to create new skills/agents/commands

Location: `~/.claude/plugins/grove/`

## Technical Approach

### Plugin Structure

```
~/.claude/plugins/grove/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── CLAUDE.md                # Plugin development guidelines
├── skills/
│   └── skill-creator/
│       └── SKILL.md         # Guide for creating new components
├── agents/                  # Empty - add as needed
└── commands/                # Empty - add as needed
```

### File Specifications

#### .claude-plugin/plugin.json

```json
{
  "name": "grove",
  "version": "0.1.0",
  "description": "A minimal, incrementally-grown Claude Code plugin"
}
```

#### CLAUDE.md

Guidelines for working with the plugin:
- How to add new skills (create `skills/<name>/SKILL.md`)
- How to add new agents (create `agents/<category>/<name>.md`)
- How to add new commands (create `commands/<name>.md`)
- Remind to bump version in plugin.json after changes

#### skills/skill-creator/SKILL.md

Meta-skill that teaches how to create:
- Skills (YAML frontmatter format, description rules, references/)
- Agents (YAML frontmatter, examples format, model inheritance)
- Commands (YAML frontmatter, $ARGUMENTS, Task syntax)

Include examples from compound_engineering as templates.

## Acceptance Criteria

- [x] Plugin loads in Claude Code without errors
- [x] `/skill-creator` invokes the skill-creator skill
- [x] CLAUDE.md explains how to extend the plugin
- [x] Empty `agents/` and `commands/` directories exist for future use

## What You'll Add Later (Reference)

When you feel the need, add these capabilities:

**Workflow orchestration:**
- `/brainstorm` - Explore requirements before planning
- `/plan` - Transform ideas into structured plans
- `/work` - Execute plans systematically

**Code review:**
- `code-reviewer` agent - Generic, language-agnostic review
- `security-reviewer` agent - Security-focused analysis

**Knowledge compounding:**
- `compound-docs` skill - Document solutions for future reference
- `docs/solutions/` directory structure

## Implementation Steps

### Phase 1: Create Plugin Skeleton

1. Create directory: `~/.claude/plugins/grove/`
2. Create `.claude-plugin/plugin.json` with minimal manifest
3. Create empty `agents/` and `commands/` directories

### Phase 2: Add CLAUDE.md

Write development guidelines covering:
- Plugin philosophy (grow organically, add when needed)
- How to create each component type
- Version bump reminder

### Phase 3: Create skill-creator Skill

Create `skills/skill-creator/SKILL.md` with:
- YAML frontmatter (name, description, allowed-tools)
- Templates for skills, agents, and commands
- Best practices from compound_engineering

### Phase 4: Verify Installation

1. Restart Claude Code or reload plugins
2. Test that grove plugin is recognized
3. Test `/skill-creator` invocation

## References

### Plugin Format Source

Analyzed from: `/Users/cullen/.claude/plugins/marketplaces/every-marketplace/plugins/compound-engineering/`

### Key Format Rules

**Skills (SKILL.md):**
- Name must match directory name
- Description must be third person ("This skill should be used when...")
- Include trigger keywords in description

**Agents:**
- Use `<example>` tags in description for when-to-use guidance
- `model: inherit` uses conversation model

**Commands:**
- Use `$ARGUMENTS` for user input
- Call agents with `Task agent-name(arguments)` syntax
