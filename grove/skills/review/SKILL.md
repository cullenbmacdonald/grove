---
name: review
description: This skill provides guidance for running parallel code review agents following Anthropic's parallelization pattern. It orchestrates agents from agents/review/ to analyze code from multiple perspectives simultaneously. Triggers on "code review", "review code", "run review", "parallel review".
---

# Review

Expert guidance for running parallel code review agents, based on Anthropic's parallelization workflow pattern.

## Core Concept

Code review benefits from multiple perspectives: security, performance, maintainability, accessibility, etc. Rather than a single pass, this skill orchestrates **parallel review agents** that each focus on a specific concern.

From Anthropic's research:

> **Parallelization - Sectioning:** Break a task into independent subtasks that run simultaneously.
> ```
> Input → [Task A] ─┐
>       → [Task B] ─┼→ Combine
>       → [Task C] ─┘
> ```
> **Best for:** Speed improvements or gaining confidence through multiple perspectives.

## Review Agents Location

Review agents live in `agents/review/`. Each agent is a markdown file that defines a focused review perspective.

## Running a Review

### Step 1: Check for Available Agents

First, check if any review agents exist:

```
Glob: agents/review/*.md
```

### Step 2: Handle No Agents Case

If no agents are found in `agents/review/`, inform the user clearly:

```markdown
No review agents found in `agents/review/`.

Create review agents that match your team's needs:
  /create-agent <focus> reviewer for <what it checks>

Examples:
  /create-agent security reviewer focused on OWASP Top 10 vulnerabilities
  /create-agent accessibility reviewer for WCAG compliance
  /create-agent API design reviewer for RESTful conventions
  /create-agent test coverage reviewer for missing unit tests

Review agents should be placed in `agents/review/` and follow the standard agent template.
See the `agent-creator` skill for detailed guidance.
```

Do NOT proceed with review if no agents exist. The user must create agents first.

### Step 3: Determine Review Scope

Based on input:

- **Specific files/directories provided**: Use those directly
- **No input provided**: Check for staged changes with `git diff --cached --name-only`
- **No staged changes**: Ask user to specify scope

### Step 4: Launch Agents in Parallel

Use the Task tool to launch all discovered review agents simultaneously. This follows Anthropic's parallelization pattern for speed and multi-perspective analysis.

```markdown
For each agent discovered in agents/review/*.md, launch using the Task tool:

Task <agent-name>("<agent-description>: [file list]")
```

**Example** - If you discovered `api-reviewer.md`, `test-coverage.md`, and `accessibility.md`:

```markdown
Task api-reviewer("Review API design: [file list]")
Task test-coverage("Check test coverage: [file list]")
Task accessibility("Review for WCAG compliance: [file list]")
```

**Key principle:** All Task calls must be in a single message to enable true parallel execution. The agent names come from what you discovered in Step 1, not a predetermined list.

### Step 5: Synthesize Results

After all agents complete, combine their findings into a unified report. The categories come from whichever agents you launched:

```markdown
## Code Review Summary

### Overview
[1-2 sentence summary of overall code quality]

### Findings by Category

#### [Agent 1 Name]
[Findings from first agent, or "No issues found"]

#### [Agent 2 Name]
[Findings from second agent, or "No issues found"]

[Continue for each agent that was launched...]

### Prioritized Recommendations

1. **Critical**: [Most important items to address]
2. **Important**: [Should be addressed soon]
3. **Consider**: [Nice-to-have improvements]

### Files Reviewed
- [List of files that were reviewed]
```

The report structure adapts to whatever agents exist. If only one agent exists, the report has one category. If five exist, it has five.

## Agent Design for Reviews

Review agents should follow these principles from Anthropic's research:

### Single-Pass Analysis Pattern

Review agents typically use the **single-pass analysis** pattern:

```markdown
You are a [specific type] reviewer.

1. Read the specified files
2. Analyze for [specific criteria]
3. Report findings in [standard format]
```

### Clear Output Format

Each agent should produce consistent output:

```markdown
## Output Format

For each finding:
- **Location**: `file:line`
- **Severity**: Critical / Warning / Info
- **Issue**: What's wrong
- **Fix**: How to resolve it
```

### Focused Scope

Each agent should have a **single, focused concern**. Examples of focused review perspectives:

- Security: vulnerabilities, auth issues, injection flaws
- Performance: bottlenecks, memory usage, algorithm efficiency
- Accessibility: WCAG compliance, screen reader support
- API design: consistency, RESTful conventions, error handling
- Test coverage: missing tests, edge cases, test quality
- i18n/l10n: hardcoded strings, locale handling
- Documentation: missing docs, outdated comments

This separation enables true parallel processing and prevents duplicate findings. Teams should create agents that match their specific needs and standards.

## Example Review Agents

These are **templates** showing the pattern. They do NOT exist by default. Create agents that match your team's actual review needs using `/create-agent`.

### Template: Focused Reviewer Pattern

```markdown
---
name: <focus-area>-reviewer
description: "Use this agent for <focus-area>-focused code review.\n\n<example>\nContext: <when this perspective is valuable>\nuser: \"/review src/...\"\nassistant: \"I'll use <focus-area>-reviewer to <what it checks>\"\n</example>"
model: inherit
---

You are a <focus-area>-focused code reviewer.

Your approach:

1. **Identify issues related to <focus-area>**
   - [Specific thing to check]
   - [Another thing to check]
   - [Third thing to check]

2. **Assess severity**
   - Critical: [What makes something critical]
   - Warning: [What makes something a warning]
   - Info: [What's just a suggestion]

3. **Report findings** with file:line references and remediation steps
```

### Example Instantiations

Here are diverse examples showing how to apply the template:

**Accessibility Reviewer** - WCAG compliance, screen reader support, keyboard navigation, color contrast

**API Design Reviewer** - RESTful conventions, error response consistency, versioning, documentation coverage

**Test Coverage Reviewer** - Missing unit tests, edge cases, test isolation, mock usage

**Security Reviewer** - OWASP Top 10, injection flaws, auth issues, sensitive data exposure

**Database Reviewer** - N+1 queries, missing indexes, transaction handling, migration safety

**i18n Reviewer** - Hardcoded strings, locale handling, RTL support, date/number formatting

Create the agents that matter for your codebase. A frontend project might want accessibility and performance agents. A backend API might want security and database agents.

## Verification

Before presenting the final review:

- [ ] Discovered all agents in `agents/review/` via Glob
- [ ] Launched all discovered agents in parallel (single message, multiple Task calls)
- [ ] Each agent's findings are included in the summary
- [ ] Findings are deduplicated across agents
- [ ] Severity levels are applied consistently
- [ ] Every finding includes a file path reference
- [ ] Recommendations are prioritized by impact

## Success Criteria

A successful review:

- [ ] Checked for agents in `agents/review/`
- [ ] If no agents: Clearly informed user and provided guidance
- [ ] If agents exist: Launched all agents in parallel
- [ ] Synthesized findings into a unified report
- [ ] Provided actionable, prioritized recommendations
