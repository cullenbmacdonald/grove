---
name: command-creator
description: This skill provides expert guidance for creating effective Claude Code slash commands. It covers frontmatter structure, workflow design, argument handling, and best practices from Anthropic's documentation. Triggers on "create command", "new command", "slash command", "write command".
---

# Command Creator

Expert guidance for creating effective Claude Code slash commands.

## Quick Start

Commands are markdown files in `.claude/commands/` that define reusable workflows invoked with `/command-name`.

**Minimal template:**

```markdown
---
name: my-command
description: What this command does (max 100 chars)
argument-hint: "[expected arguments]"
---

# My Command

## Input

<input> #$ARGUMENTS </input>

## Steps

1. First step
2. Second step

## Success Criteria

- [ ] Expected outcome
```

## Required: YAML Frontmatter

Every command MUST start with YAML frontmatter:

```yaml
---
name: command-name
description: Brief description (max 100 chars)
argument-hint: "[what arguments are expected]"
---
```

**Field requirements:**
- `name`: Lowercase letters, numbers, hyphens only (max 64 chars)
- `description`: Clear, concise summary of command purpose
- `argument-hint`: Shows user what to provide (e.g., `[file path]`, `[PR number]`)

## Command Design Principles

### 1. Set Appropriate Degrees of Freedom

Match specificity to task fragility:

**High freedom** (multiple valid approaches):
```markdown
## Review the code for potential issues

Analyze structure, check for bugs, suggest improvements.
```

**Low freedom** (exact sequence required):
```markdown
## Deploy steps

Run exactly these commands in order:
1. `npm run test`
2. `npm run build`
3. `npm run deploy`

Do not skip or reorder steps.
```

### 2. Use Structured Arguments

Reference user input with `$ARGUMENTS` or indexed access:

```markdown
## Input

<input> #$ARGUMENTS </input>

Implement feature: $ARGUMENTS[0]
In directory: $ARGUMENTS[1]
```

**Shorthand:** `$0`, `$1`, `$2` work identically to `$ARGUMENTS[0]`, etc.

### 3. Leverage Claude's Tools

**File Operations:**
- `Read`, `Edit`, `Write` - file modifications
- `Glob`, `Grep` - search codebase

**Development:**
- `Bash` - run commands (git, tests, linters)
- `Task` - launch specialized agents

**Web & APIs:**
- `WebFetch`, `WebSearch` - research
- `gh` CLI - GitHub operations

### 4. Include Verification Steps

Always verify results:

```markdown
## Verification

- Run tests: `npm test`
- Lint code: `npm run lint`
- Check changes: `git diff`
```

## Command Patterns

For detailed patterns and examples, see:
- [references/patterns.md](references/patterns.md) - Common command patterns
- [references/tools-guide.md](references/tools-guide.md) - Available tools and integrations

## Creating the Command

1. Create file at `.claude/commands/[name].md`
2. Start with YAML frontmatter
3. Define clear steps
4. Add success criteria
5. Test with real arguments

## Anti-Patterns to Avoid

- Vague instructions without specific actions
- Missing verification steps
- Overly complex multi-path workflows
- Assuming context not provided
- Windows-style paths (use forward slashes)

## Success Criteria

A well-designed command:
- [ ] Has complete YAML frontmatter
- [ ] States clear purpose and scope
- [ ] Provides step-by-step workflow
- [ ] Includes verification/success criteria
- [ ] Handles arguments appropriately
- [ ] References specific tools to use
