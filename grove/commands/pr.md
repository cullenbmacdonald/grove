---
name: grove:pr
description: Run checks, update docs, and create or update a PR
argument-hint: "[optional: PR title or description context]"
allowed-tools: Read, Edit, Write, Glob, Grep, Bash, Agent
---

# Create or Update PR

Run repo checks, update relevant documentation from the diff, and create or update a pull request.

## Input

<input> #$ARGUMENTS </input>

## Instructions

Load the `pr` skill for the full workflow. Follow these steps in order:

### 1. Verify Branch State

- Confirm you're on a feature branch (not main/master)
- Check for uncommitted changes - if any exist, ask the user whether to commit first
- Determine the base branch from the remote default

### 2. Run Repo Checks

Detect and run pre-commit and pre-push hooks/tests for this repo. Stop on failure.

### 3. Analyze and Update Docs

- Get the full diff against the base branch
- Review commit history on this branch
- Read existing `README.md`, `CLAUDE.md`, `docs/*.md`, `CHANGELOG.md`
- Update only files where the PR changes are genuinely relevant
- Commit doc updates separately if any were made

### 4. Push and PR

- Push the branch
- Check if a PR already exists for this branch
- Create a new PR or update the existing one
- If user provided arguments, use them as context for the PR title/description
- Return the PR URL

## Output

End with the PR URL and a brief summary of what was done (checks run, docs updated, PR created/updated).
