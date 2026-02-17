---
name: grove:tasks
description: Show current tasks — due, overdue, or all open
argument-hint: "[today|all|week|high|search term]"
allowed-tools: Read, Glob, Grep, Bash
---

Show open tasks from the vault.

**Filter:** $ARGUMENTS

## Instructions

0. Get the vault path by running: `source ~/.claude/plugins/*/claude_garden/.env 2>/dev/null || source ~/dev/claude_garden/.env && echo $OBSIDIAN_VAULT_PATH`. Use this as `VAULT_PATH` in all subsequent steps. If it's empty, tell the user to set `OBSIDIAN_VAULT_PATH` in their `.env` file.

1. Today's date is {{currentDate}}.

2. Read all markdown files in `VAULT_PATH/02 - areas/tasks/`.

3. Parse the YAML frontmatter of each file for `status`, `due`, `priority`, and `related`.

4. Filter based on the user's argument:
   - No argument or "today": Show tasks that are **due today or overdue** (due date <= today), plus any with **no due date**. Group overdue separately.
   - "all": Show **all open tasks** (status: todo or in-progress), sorted by due date (soonest first), then tasks with no due date.
   - "week": Show tasks due within the **next 7 days**, plus overdue.
   - "high" or "priority": Show only **high priority** open tasks.
   - Any other text: Search task titles and note bodies for that text.

5. Display as a clean list:

```
**Overdue**
- [ ] Task title (due Feb 10) — [[related note]]
- [ ] Task title (due Feb 14, high)

**Due Today**
- [ ] Task title

**Upcoming**
- [ ] Task title (due Feb 20)

**No Due Date**
- [ ] Task title
```

   - Only show sections that have tasks in them
   - Include priority only if set
   - Include related links if present
   - If no tasks match, say "No tasks found."

6. At the end, show a count: "X open tasks (Y overdue)"
