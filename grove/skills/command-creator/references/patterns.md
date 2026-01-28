# Command Patterns Reference

Common patterns for effective Claude Code commands.

## Pattern 1: Research-Then-Implement

For tasks requiring codebase understanding before changes:

```markdown
---
name: implement-feature
description: Research existing patterns then implement a feature
argument-hint: "[feature description]"
---

# Implement Feature

## Input

<input> #$ARGUMENTS </input>

## Steps

### 1. Research Phase

- Search for similar code using Grep
- Read relevant files to understand patterns
- Identify conventions and style

### 2. Planning Phase

- Think through edge cases
- Determine test cases needed
- Plan file structure

### 3. Implementation

- Follow existing code patterns
- Write tests alongside implementation
- Ensure code matches project conventions

### 4. Verification

- Run tests: `[test command]`
- Run linter: `[lint command]`
- Review changes with git diff

## Success Criteria

- [ ] Tests pass
- [ ] Linting clean
- [ ] Follows project patterns
```

## Pattern 2: Workflow Automation

For repetitive multi-step processes:

```markdown
---
name: release
description: Create a release with changelog and tag
argument-hint: "[version]"
---

# Release Workflow

## Input

<version> #$ARGUMENTS </version>

**If empty**, determine version by analyzing recent commits.

## Steps

### 1. Pre-flight Checks

- Ensure on main branch: `git branch --show-current`
- Ensure working directory clean: `git status`
- Run full test suite

### 2. Update Version

- Update version in package.json
- Update CHANGELOG.md with recent changes

### 3. Create Release

- Commit changes: "chore: release v[version]"
- Create tag: `git tag v[version]`

### 4. Verification

- Verify tag: `git tag -l`
- Verify commit message

## Success Criteria

- [ ] Tests pass
- [ ] Version updated
- [ ] Changelog updated
- [ ] Tag created
```

## Pattern 3: Code Review

For structured analysis tasks:

```markdown
---
name: review
description: Review code changes for issues
argument-hint: "[files or commit range]"
---

# Code Review

## Scope

<scope> #$ARGUMENTS </scope>

**Default:** Review staged changes if no scope provided.

## Review Areas

### 1. Correctness

- Logic errors
- Edge cases
- Off-by-one errors
- Null/undefined handling

### 2. Security

- Input validation
- Authentication/authorization
- Data exposure
- Injection vulnerabilities

### 3. Performance

- Unnecessary loops
- Missing indexes
- Memory leaks
- N+1 queries

### 4. Maintainability

- Code clarity
- Documentation needs
- Test coverage
- Naming conventions

## Output Format

Provide findings as:

```markdown
## Review Summary

### Critical Issues
- [issue description with file:line reference]

### Suggestions
- [improvement suggestion]

### Positive Notes
- [well-done aspects]
```
```

## Pattern 4: Investigation/Debug

For troubleshooting and analysis:

```markdown
---
name: investigate
description: Investigate an issue or error
argument-hint: "[error message or issue description]"
---

# Investigation

## Problem

<problem> #$ARGUMENTS </problem>

## Approach

### 1. Gather Context

- Search for error message in codebase
- Find related log entries
- Identify affected code paths

### 2. Reproduce

- Determine reproduction steps
- Identify minimal test case

### 3. Analyze

- Trace execution flow
- Identify root cause
- Determine scope of impact

### 4. Propose Solution

- Describe fix approach
- Identify files to modify
- Consider side effects

## Output

Provide:
1. Root cause analysis
2. Proposed fix
3. Test plan to verify
```

## Pattern 5: Template Generation

For creating standardized outputs:

```markdown
---
name: new-component
description: Create a new component from template
argument-hint: "[ComponentName]"
---

# New Component

## Input

<name> #$ARGUMENTS </name>

## Template

Create `src/components/$ARGUMENTS/$ARGUMENTS.tsx`:

```tsx
import React from 'react';

interface ${ARGUMENTS}Props {
  // Define props here
}

export const $ARGUMENTS: React.FC<${ARGUMENTS}Props> = (props) => {
  return (
    <div>
      {/* Component content */}
    </div>
  );
};
```

Create `src/components/$ARGUMENTS/$ARGUMENTS.test.tsx`:

```tsx
import { render, screen } from '@testing-library/react';
import { $ARGUMENTS } from './$ARGUMENTS';

describe('$ARGUMENTS', () => {
  it('renders correctly', () => {
    render(<$ARGUMENTS />);
    // Add assertions
  });
});
```

## Success Criteria

- [ ] Component file created
- [ ] Test file created
- [ ] Exports added to index
```

## Pattern 6: Conditional Workflow

For commands with branching logic:

```markdown
---
name: fix
description: Fix an issue based on type
argument-hint: "[issue type] [details]"
---

# Fix Issue

## Input

<issue> #$ARGUMENTS </issue>

## Determine Fix Type

1. If `$0` is "lint" → Follow lint fix workflow
2. If `$0` is "test" → Follow test fix workflow
3. If `$0` is "type" → Follow type error workflow
4. Otherwise → General debugging workflow

## Lint Fix Workflow

- Run linter with auto-fix: `npm run lint:fix`
- Review changes
- Run tests to verify no regressions

## Test Fix Workflow

- Run failing tests to see error
- Read test file and implementation
- Fix the underlying issue
- Verify all tests pass

## Type Error Workflow

- Run type checker to see errors
- Read relevant type definitions
- Fix type issues
- Verify no type errors remain

## General Debugging

- Reproduce the issue
- Add logging if needed
- Fix the root cause
- Add test to prevent regression
```

## Tips for Effective Commands

### Be Explicit About Constraints

```markdown
**Constraints:**
- Do not modify files in `vendor/`
- Use existing database migrations pattern
- Follow TypeScript strict mode
```

### Use XML Tags for Structure

```markdown
<task>
Implement user authentication
</task>

<requirements>
- Support email/password login
- Include password reset
- Add session management
</requirements>

<constraints>
- Use existing auth library
- Follow security best practices
</constraints>
```

### Include Thinking Keywords

For complex problems, prompt deeper reasoning:

```markdown
Think carefully about edge cases before implementing.
Plan the approach thoroughly.
```

### Reference CLAUDE.md

```markdown
Follow conventions defined in CLAUDE.md for:
- File naming
- Code style
- Test patterns
```
