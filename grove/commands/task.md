---
name: grove:task
description: Create a new task note
argument-hint: "[task description, optional due date and priority]"
allowed-tools: Read, Write, Bash
---

Create a new task note in the vault.

**Task input:** $ARGUMENTS

## Instructions

0. Get the vault path by running: `source ~/.claude/plugins/*/claude_garden/.env 2>/dev/null || source ~/dev/claude_garden/.env && echo $OBSIDIAN_VAULT_PATH`. Use this as `VAULT_PATH` in all subsequent steps. If it's empty, tell the user to set `OBSIDIAN_VAULT_PATH` in their `.env` file.

1. Parse the input for:
   - **Task title**: The main description of what needs to be done
   - **Due date**: Look for phrases like "by Friday", "due 2026-02-20", "tomorrow", "next week", etc. If found, convert to YYYY-MM-DD. If not found, omit the `due` field.
   - **Priority**: Look for "high", "low", or "urgent". Default to blank (no priority field) if not specified. "urgent" maps to "high".
   - **Context links**: Look for mentions of people, projects, or areas that map to notes in the vault. Add as `[[wikilinks]]` in the `related` field.

2. Today's date is {{currentDate}}.

3. Generate a filename-safe slug from the task title (lowercase, hyphens, no special chars). The file path is:
   `VAULT_PATH/02 - areas/tasks/YYYY-MM-DD-slug.md`
   where YYYY-MM-DD is today's date (created date, not due date).

4. Create the task note with this structure:

```
---
type: task
status: todo
created: YYYY-MM-DD
due: YYYY-MM-DD
priority: high|low
related:
  - "[[Some Note]]"
---

# Task Title

## Notes

```

   - Omit `due` if no due date was given
   - Omit `priority` if none was specified
   - Omit `related` if no context links were identified
   - The `## Notes` section is for future context — leave it empty

5. Confirm with a one-line response like: "Created: Task Title (due Feb 20)" or "Created: Task Title" if no due date. Nothing more.
