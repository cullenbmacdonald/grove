# Claude Garden

Personal Claude Code plugin marketplace. Contains the **grove** plugin for scaffolding skills, commands, and agents — plus Obsidian vault tooling.

## Install

Add this repo as a plugin marketplace in Claude Code:

```bash
claude plugin marketplace add https://github.com/cullenbmacdonald/grove
```

Then install the grove plugin:

```bash
claude plugin install grove
```

## Setup

Create `~/.grove.env` with your Obsidian vault path:

```bash
echo 'OBSIDIAN_VAULT_PATH=/Users/yourname/path/to/vault' > ~/.grove.env
```

## Commands

### Obsidian

| Command | Description |
|---------|-------------|
| `/grove:log [entry]` | Append a timestamped entry to today's daily note |
| `/grove:task [description]` | Create a new task note (parses due dates, priority) |
| `/grove:tasks [filter]` | Show open tasks — `today`, `all`, `week`, `high`, or search |
| `/grove:done [search]` | Mark a task as done by title or search term |

### Scaffolding

| Command | Description |
|---------|-------------|
| `/grove:create-command` | Create a new slash command |
| `/grove:create-agent` | Create a new agent |
| `/grove:create-skill` | Create a new skill |
| `/grove:plan` | Create a plan using parallel agents |
| `/grove:review` | Run parallel code review |

## New Machine Setup

1. Run the install commands above
2. Create `~/.grove.env` with `OBSIDIAN_VAULT_PATH=/path/to/your/vault`
