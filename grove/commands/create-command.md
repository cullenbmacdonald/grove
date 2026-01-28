---
name: create-command
description: Create a new custom slash command following conventions and best practices
argument-hint: "[command purpose and requirements]"
---

# Create a Custom Claude Code Command

Create a new slash command in `.claude/commands/` for the requested task.

## Goal

#$ARGUMENTS

## Instructions

Load the `command-creator` skill for detailed guidance on command structure, patterns, and best practices.

## Quick Reference

### Required YAML Frontmatter

```yaml
---
name: command-name
description: Brief description (max 100 chars)
argument-hint: "[expected arguments]"
---
```

### Command Structure

```markdown
# Command Title

## Input

<input> #$ARGUMENTS </input>

## Steps

1. [First step with specific details]
2. [Second step]
3. [Verification step]

## Success Criteria

- [ ] Expected outcome 1
- [ ] Expected outcome 2
```

### Key Principles

1. **Set appropriate freedom** - Match specificity to task fragility
2. **Use `$ARGUMENTS`** - Reference user input with `$ARGUMENTS`, `$0`, `$1`, etc.
3. **Include verification** - Tests, linting, git diff
4. **Be explicit** - Clear constraints and tool references

### Creating the File

1. Create at `.claude/commands/[name].md`
2. Start with YAML frontmatter
3. Define clear steps with verification
4. Test with real arguments

For detailed patterns and examples, see the `command-creator` skill.
