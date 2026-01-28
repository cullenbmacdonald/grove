# Available Tools Guide

Reference for tools available in Claude Code commands.

## File Operations

### Read

Read file contents. Use for understanding existing code.

```markdown
Read the authentication module to understand patterns.
```

### Edit

Make precise changes to existing files.

```markdown
Update the validateUser function to include email validation.
```

### Write

Create new files or overwrite existing ones.

```markdown
Create a new utility file at src/utils/validation.ts.
```

### Glob

Find files by pattern.

```markdown
Find all TypeScript files: `**/*.ts`
Find test files: `**/*.test.ts`
Find configs: `**/config.*`
```

### Grep

Search file contents with regex.

```markdown
Search for TODO comments: `TODO|FIXME`
Find function definitions: `function \w+`
Find imports: `import.*from`
```

## Development Tools

### Bash

Execute shell commands. Common uses:

**Git operations:**
```markdown
- Check status: `git status`
- View diff: `git diff`
- View log: `git log --oneline -10`
- Create branch: `git checkout -b feature/name`
```

**Package management:**
```markdown
- Install deps: `npm install` / `pip install`
- Run scripts: `npm run [script]`
- Run tests: `npm test` / `pytest`
```

**Linting/formatting:**
```markdown
- Lint: `npm run lint` / `eslint .`
- Format: `npm run format` / `prettier --write .`
- Type check: `npm run typecheck` / `tsc --noEmit`
```

### Task

Launch specialized agents for complex subtasks:

```markdown
Task Explore("Find all API endpoints in the codebase")
Task code-reviewer("Review the authentication changes")
```

**Built-in agent types:**
- `Explore` - Codebase exploration
- `Plan` - Implementation planning
- `general-purpose` - Multi-step tasks

## Web & Research Tools

### WebFetch

Fetch and analyze web content:

```markdown
Fetch the API documentation at [URL] and summarize key endpoints.
```

### WebSearch

Search the web for information:

```markdown
Search for "React useEffect best practices 2026"
```

## GitHub Integration (gh CLI)

Use `gh` for all GitHub operations:

**Pull Requests:**
```bash
gh pr list
gh pr view [number]
gh pr create --title "..." --body "..."
gh pr merge [number]
gh pr diff [number]
```

**Issues:**
```bash
gh issue list
gh issue view [number]
gh issue create --title "..." --body "..."
gh issue close [number]
```

**Reviews:**
```bash
gh pr review [number] --approve
gh pr review [number] --request-changes --body "..."
gh api repos/{owner}/{repo}/pulls/{number}/comments
```

## Tool Selection Guide

| Task | Recommended Tool |
|------|------------------|
| Read a file | Read |
| Find files by name | Glob |
| Search file contents | Grep |
| Make targeted edits | Edit |
| Create new files | Write |
| Run commands | Bash |
| Complex analysis | Task (Explore) |
| GitHub operations | Bash (gh) |
| Web research | WebSearch, WebFetch |

## Best Practices

### Prefer Specific Tools

**Good:**
```markdown
Use Grep to find all usages of validateUser function.
```

**Avoid:**
```markdown
Find all usages (unclear which tool to use).
```

### Combine Tools Effectively

```markdown
1. Glob to find relevant files: `src/**/*.service.ts`
2. Grep to search within those files: `async.*validate`
3. Read the most relevant matches
4. Edit to make changes
```

### Use Tool Outputs in Verification

```markdown
## Verification

After changes:
- Bash: `npm test` (verify tests pass)
- Bash: `npm run lint` (verify no lint errors)
- Bash: `git diff` (review changes)
```

### Handle Tool Failures

```markdown
If tests fail:
1. Read the test output
2. Identify the failing test
3. Fix the underlying issue
4. Re-run tests

Do not proceed until tests pass.
```
