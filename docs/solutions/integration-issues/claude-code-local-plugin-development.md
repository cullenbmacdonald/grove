---
title: Claude Code Local Plugin Development Setup
category: integration-issues
tags: [claude-code, plugins, development-workflow]
symptoms:
  - Plugin works in project directory but not globally
  - Symlink to ~/.claude/plugins doesn't work
  - Plugin not recognized from other directories
module: grove
date: 2026-01-26
---

# Claude Code Local Plugin Development Setup

## Problem

When developing a Claude Code plugin, you want:
1. Source code in a git repo for version control
2. Plugin available globally (from any directory)
3. Changes reflected immediately without manual sync

Initial attempts that **don't work**:
- Symlinking plugin to `~/.claude/plugins/` - not recognized
- Adding to `~/.claude/plugins/marketplaces/` - breaks Claude or not recognized
- Modifying `known_marketplaces.json` - can break Claude startup

## Root Cause

Claude Code's plugin discovery for `~/.claude/plugins/` is designed for **installed** plugins from marketplaces, not development plugins. The internal registry files (`installed_plugins.json`, `known_marketplaces.json`) have specific schemas that aren't meant for manual editing.

## Solution

Use the `--plugin-dir` flag to load your plugin from any location:

```bash
claude --plugin-dir /path/to/your/plugin
```

For convenience, create a shell alias:

```bash
# Add to ~/.zshrc or ~/.bashrc
alias claude='claude --plugin-dir /path/to/your/plugin'
```

Then `source ~/.zshrc` or restart your terminal.

### Multi-Machine Setup

1. Keep plugin source in a git repo
2. Clone repo on each machine
3. Add the alias pointing to the local clone path

```bash
# Machine 1
alias claude='claude --plugin-dir ~/dev/my-repo/my-plugin'

# Machine 2 (same alias, same relative path)
alias claude='claude --plugin-dir ~/dev/my-repo/my-plugin'
```

## Directory Structure

```
~/dev/my-repo/           # Git repo root
├── docs/                # Project docs (brainstorms, plans, solutions)
├── my-plugin/           # Plugin source
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── skills/
│   ├── agents/
│   ├── commands/
│   └── CLAUDE.md
└── README.md
```

## Why This Works Better

- **Clean separation**: Installed plugins vs development plugins
- **No internal file hacking**: Don't touch Claude's registry files
- **Immediate changes**: Edit files, changes apply next session
- **Git-friendly**: Source of truth is your repo, not ~/.claude/
- **Multi-machine**: Same workflow everywhere via alias

## Prevention

When starting a new Claude Code plugin:

1. Create plugin in a git repo (not in ~/.claude/)
2. Set up the alias immediately
3. Don't attempt symlinks or marketplace hacks

## Related

- [Claude Code plugins documentation](https://docs.anthropic.com/claude-code/plugins)
