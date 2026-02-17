---
name: grove:log
description: Quickly log an entry to today's daily note
argument-hint: "[entry text]"
allowed-tools: Read, Edit, Write, Bash
---

Append a timestamped journal entry to today's daily note.

**Entry to log:** $ARGUMENTS

## Instructions

0. Get the vault path by running: `source ~/.claude/plugins/*/claude_garden/.env 2>/dev/null || source ~/dev/claude_garden/.env && echo $OBSIDIAN_VAULT_PATH`. Use this as `VAULT_PATH` in all subsequent steps. If it's empty, tell the user to set `OBSIDIAN_VAULT_PATH` in their `.env` file.

1. Today's date is {{currentDate}}. Format it as YYYY-MM-DD for the filename.
2. The daily note path is: `VAULT_PATH/02 - areas/daily/YYYY-MM-DD.md`
3. Read today's daily note. If it doesn't exist, create it with this structure:

```
# Journal


## Morning Checks
- [ ] look at calendar
- [ ] Recruiting tasks
- [ ] Risk
- [ ] Benefits


## Tasks


## Notes
```

4. Get the current time using `date +"%I:%M %p"` (12-hour format).
5. Append the log entry as a bullet under `# Journal`, BEFORE the `## Morning Checks` section. Use this format:

```
- **12:34 PM** — the entry text here
```

6. If there are already entries under `# Journal`, add the new entry as the last bullet before `## Morning Checks`. Always leave one blank line between the last entry and `## Morning Checks`.
7. Keep the entry exactly as the user wrote it. Don't rephrase, summarize, or embellish.
8. After appending, confirm with a very brief one-line response like: "Logged ✓" — nothing more.
