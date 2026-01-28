# Custom Claude Code Plugin - Brainstorm

**Date:** 2026-01-26
**Status:** Ready for planning

## What We're Building

A minimal, incrementally-grown Claude Code plugin that replaces compound_engineering with only the features you actually need. Starting from an "ultra-minimal seed" approach: just the plugin skeleton and the ability to create new skills/agents.

## Why This Approach

The compound_engineering plugin has 28 agents, 24 commands, and 15 skills. Most are specialized for:
- Rails/Ruby (you don't use)
- Every.to's style guide (organization-specific)
- Figma design workflows (you don't need)
- iOS/Xcode testing (you don't need)

Your stack: TypeScript/JavaScript, Python, Go, Bash, infrastructure

**Philosophy:** Add capabilities when you feel the pain of not having them, not before.

## Key Decisions

1. **Start with plugin skeleton only** - Learn the structure by building it
2. **Include skill-creator skill** - Meta-skill to bootstrap everything else
3. **Document solutions as you go** - Build your own knowledge base organically
4. **Language-agnostic agents** - When you add review agents, make them work across your stack

## What You'll Build First (The Seed)

```
your-plugin/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── skills/
│   └── skill-creator/
│       └── SKILL.md         # Guide for creating new skills
├── agents/                  # Empty, add as needed
├── commands/                # Empty, add as needed
└── CLAUDE.md               # Your plugin's guidelines
```

## What You'll Add Later (When Needed)

**When you want workflow orchestration:**
- `/brainstorm` command
- `/plan` command
- `/work` command

**When you want code review:**
- `code-reviewer` agent (generic, multi-language)
- `security-reviewer` agent

**When you want knowledge compounding:**
- `compound-docs` skill
- `docs/solutions/` directory structure

## Foundational Concepts to Understand

From compound_engineering research:

### Plugin Structure
- `plugin.json` - Metadata, MCP servers, version
- `CLAUDE.md` - Guidelines Claude follows in your project
- `agents/` - Specialized Task subagents
- `skills/` - Domain knowledge loaded on-demand (SKILL.md files)
- `commands/` - Slash commands (markdown files)

### How Skills Work
- 3-level loading: metadata → SKILL.md body → references
- Triggered by keywords or explicit `/skill-name` invocation
- Provide context and instructions, not code execution

### How Agents Work
- Defined in Task tool's subagent_type descriptions
- Run as autonomous subprocesses with specific tool access
- Can run in parallel for efficiency

### How Commands Work
- Markdown files with instructions
- Invoked via `/command-name`
- Orchestrate skills and agents

## Open Questions

1. What should you name your plugin?
2. Do you want MCP server integration (like context7 for docs)?
3. Where will you install it? (`~/.claude/plugins/` or project-local?)

## Next Steps

1. Run `/workflows:plan` to create implementation plan
2. Create plugin skeleton
3. Add skill-creator skill
4. Use it to add capabilities as you need them
