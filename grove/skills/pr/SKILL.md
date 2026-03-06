---
name: pr
description: This skill should be used when creating or updating a pull request with automatic doc updates and hook validation. It runs pre-commit/pre-push checks, updates relevant documentation to reflect changes, and creates or updates a PR. Triggers on "create pr", "update pr", "open pr", "pull request", "push and pr".
---

# Create or Update PR

Workflow skill for shipping changes: run repo hooks, update documentation to reflect the diff, push, and create or update a PR.

## Overview

This skill orchestrates the full PR lifecycle from any repo:

1. **Validate** - Run pre-commit and pre-push hooks
2. **Document** - Analyze the diff and update relevant docs
3. **Ship** - Push and create or update the PR

## Workflow

### Step 1: Ensure Clean State

Check that all changes are committed. If there are uncommitted changes, stop and ask the user whether to commit them first or proceed with only what's committed.

```
git status
git diff --cached --quiet  # Check for staged but uncommitted
git diff --quiet           # Check for unstaged changes
```

### Step 2: Identify the Branch and Base

Determine the current branch and the base branch for the PR.

```
git branch --show-current
git rev-parse --abbrev-ref HEAD
```

If on `main` or `master`, stop and tell the user they need to be on a feature branch.

Detect the default branch for the repo:

```
git remote show origin | grep 'HEAD branch' | awk '{print $NF}'
```

Use that as the base branch.

### Step 3: Run Pre-Commit Hooks

Run the repo's pre-commit checks. Detect the mechanism used:

1. **`.pre-commit-config.yaml`** - Run `pre-commit run --all-files`
2. **`.husky/pre-commit`** - Run `.husky/pre-commit`
3. **`package.json` with lint-staged** - Run `npx lint-staged`
4. **Makefile with `lint` or `check` target** - Run `make lint` or `make check`
5. **`.git/hooks/pre-commit`** - Run `.git/hooks/pre-commit`

If hooks fail, report the errors and stop. Do NOT skip hooks or use `--no-verify`.

### Step 4: Run Pre-Push Hooks

Similarly, detect and run pre-push checks:

1. **`.husky/pre-push`** - Run `.husky/pre-push`
2. **`.git/hooks/pre-push`** - Run `.git/hooks/pre-push`
3. **`package.json` with `test` script** - Run `npm test` or equivalent
4. **Makefile with `test` target** - Run `make test`

If no explicit pre-push hooks exist, look for a test command and run it. If no tests exist either, note this and continue.

If hooks fail, report the errors and stop.

### Step 5: Analyze the Diff for Documentation Updates

This is the key step. Gather the full diff against the base branch:

```
git diff <base-branch>...HEAD
```

Also review the commit messages on this branch:

```
git log <base-branch>..HEAD --oneline
```

Analyze the diff and commits for:

- **New features or capabilities** added
- **Changed behavior** or API modifications
- **Configuration changes** (new env vars, changed defaults)
- **Architectural decisions** visible in the code
- **Gotchas or non-obvious choices** (workarounds, compatibility hacks, known limitations)
- **Removed functionality**

### Step 6: Update Documentation

Check which documentation files exist in the repo:

```
# Check for docs
ls docs/*.md 2>/dev/null
ls README.md 2>/dev/null
ls CLAUDE.md 2>/dev/null
ls CHANGELOG.md 2>/dev/null
```

For each existing doc file, read it and determine if the PR's changes are relevant to its content. Update only files where the changes are genuinely relevant:

- **`README.md`** - Update if new features, changed setup steps, modified API, or altered usage patterns
- **`CLAUDE.md`** - Update if architectural decisions, new conventions, changed project structure, or important gotchas that future Claude sessions should know
- **`docs/*.md`** - Update specific docs that cover areas touched by the PR
- **`CHANGELOG.md`** - Add entry if the file exists and follows a changelog convention

**Rules for doc updates:**

- Do NOT create doc files that don't already exist (except adding a CHANGELOG entry if a CHANGELOG exists)
- Do NOT add boilerplate or filler - only substantive, useful content
- Match the existing style and format of each doc
- Keep updates concise - a few lines, not paragraphs
- If no docs need updating, skip this step entirely and say so

### Step 7: Commit Documentation Updates

If any docs were updated, commit them separately:

```
git add <updated-doc-files>
git commit -m "docs: update documentation to reflect PR changes"
```

### Step 8: Push

Push the branch to the remote:

```
git push -u origin <branch-name>
```

If the push is rejected (e.g., remote has new commits), stop and inform the user rather than force-pushing.

### Step 9: Create or Update PR

Check if a PR already exists for this branch:

```
gh pr view --json number,title,body 2>/dev/null
```

**If a PR exists:** Update it if the title/body no longer reflect the changes. Use `gh pr edit` to update title and body.

**If no PR exists:** Create one with `gh pr create`.

#### PR Title

- Keep under 70 characters
- Use conventional format if the repo uses it (feat:, fix:, etc.)
- Summarize the change, not the implementation

#### PR Body

Generate from the diff analysis in Step 5:

```markdown
## Summary
[2-4 bullet points describing what changed and why]

## Changes
[Key changes grouped logically]

## Documentation Updated
[List any docs that were updated in Step 6, or "No documentation changes needed"]

## Test Plan
- [ ] [How to verify the changes work]

Generated with [Claude Code](https://claude.com/claude-code)
```

## Important Notes

- This skill works from **any repo**, not just grove
- Always respect the repo's existing hooks and checks - never bypass them
- Documentation updates should be **genuinely useful**, not mechanical
- When in doubt about a doc update, skip it - don't add noise
- Always confirm with the user before pushing and creating/updating the PR

## Success Criteria

- [ ] Pre-commit hooks passed (or none exist)
- [ ] Pre-push hooks / tests passed (or none exist)
- [ ] Diff analyzed for documentation-relevant changes
- [ ] Relevant existing docs updated (if any changes warranted)
- [ ] Doc updates committed separately
- [ ] Branch pushed to remote
- [ ] PR created or updated with descriptive title and body
- [ ] PR URL returned to the user
