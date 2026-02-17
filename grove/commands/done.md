---
name: grove:done
description: Mark a task as done
argument-hint: "[task title or search term]"
allowed-tools: Read, Edit, Glob, Grep, Bash
---

Mark a task as done.

**Search term:** $ARGUMENTS

## Instructions

0. Get the vault path by running: `source ~/.claude/plugins/*/claude_garden/.env 2>/dev/null || source ~/dev/claude_garden/.env && echo $OBSIDIAN_VAULT_PATH`. Use this as `VAULT_PATH` in all subsequent steps. If it's empty, tell the user to set `OBSIDIAN_VAULT_PATH` in their `.env` file.

1. Today's date is {{currentDate}}.

2. Search task files in `VAULT_PATH/02 - areas/tasks/` for the user's input. Match against:
   - The filename
   - The `# Title` in the file
   - The body content

3. If **one match** is found, update its frontmatter:
   - Change `status: todo` (or `in-progress`) to `status: done`
   - Add `completed: YYYY-MM-DD` (today's date)

4. If **multiple matches** are found, list them and ask the user to be more specific.

5. If **no matches** are found, say "No matching task found."

6. Confirm with: "Done: Task Title" — nothing more.
