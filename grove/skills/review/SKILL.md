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

To create review agents, use:
  /create-agent security reviewer focused on OWASP Top 10 vulnerabilities
  /create-agent performance reviewer for identifying bottlenecks
  /create-agent accessibility checker for WCAG compliance

Review agents should be placed in `agents/review/` and follow the standard agent template.
See the `agent-creator` skill for detailed guidance on creating effective agents.
```

Do NOT proceed with review if no agents exist. The user must create agents first.

### Step 3: Determine Review Scope

Based on input:

- **Specific files/directories provided**: Use those directly
- **No input provided**: Check for staged changes with `git diff --cached --name-only`
- **No staged changes**: Ask user to specify scope

### Step 4: Launch Agents in Parallel

Use the Task tool to launch all review agents simultaneously. This follows Anthropic's parallelization pattern for speed and multi-perspective analysis.

```markdown
Launch all agents in a single message with multiple Task tool calls:

Task security-reviewer("Review these files for security issues: [file list]")
Task performance-reviewer("Review these files for performance issues: [file list]")
Task style-reviewer("Review these files for style and maintainability: [file list]")
```

**Key principle:** All Task calls should be in a single message to enable true parallel execution.

### Step 5: Synthesize Results

After all agents complete, combine their findings into a unified report:

```markdown
## Code Review Summary

### Overview
[1-2 sentence summary of overall code quality]

### Findings by Category

#### Security
[Findings from security reviewer, or "No issues found"]

#### Performance
[Findings from performance reviewer, or "No issues found"]

#### [Other Categories...]
[Findings from other reviewers]

### Prioritized Recommendations

1. **Critical**: [Most important items to address]
2. **Important**: [Should be addressed soon]
3. **Consider**: [Nice-to-have improvements]

### Files Reviewed
- [List of files that were reviewed]
```

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

Each agent should have a **single, focused concern**:

- Security agent focuses on vulnerabilities, not style
- Performance agent focuses on bottlenecks, not security
- Style agent focuses on maintainability, not performance

This separation enables true parallel processing and prevents duplicate findings.

## Example Review Agents

These are examples of what agents in `agents/review/` might look like. They do NOT exist by default.

### Security Reviewer

```markdown
---
name: security-reviewer
description: "Use this agent for security-focused code review.\n\n<example>\nContext: Reviewing a PR with authentication changes\nuser: \"/review src/auth/\"\nassistant: \"I'll use security-reviewer to check for vulnerabilities\"\n<commentary>\nAuth code requires security-focused review for vulnerabilities.\n</commentary>\n</example>"
model: inherit
---

You are a security-focused code reviewer specializing in OWASP Top 10 vulnerabilities.

Your approach:

1. **Scan for vulnerabilities**
   - Injection flaws (SQL, command, XSS)
   - Authentication/authorization issues
   - Sensitive data exposure
   - Security misconfigurations

2. **Assess severity**
   - Critical: Exploitable with high impact
   - Warning: Potential vulnerability
   - Info: Security best practice suggestion

3. **Report findings** with file:line references and remediation steps
```

### Performance Reviewer

```markdown
---
name: performance-reviewer
description: "Use this agent for performance-focused code review.\n\n<example>\nContext: Reviewing data processing code\nuser: \"/review src/data/\"\nassistant: \"I'll use performance-reviewer to identify bottlenecks\"\n<commentary>\nData processing code benefits from performance analysis.\n</commentary>\n</example>"
model: inherit
---

You are a performance-focused code reviewer.

Your approach:

1. **Identify bottlenecks**
   - O(n²) or worse algorithms
   - Unnecessary iterations
   - Missing caching opportunities
   - Blocking operations

2. **Check resource usage**
   - Memory allocation patterns
   - Database query efficiency
   - Network call optimization

3. **Report findings** with specific optimization suggestions
```

## Verification

Before presenting the final review:

- [ ] All available review agents were launched in parallel
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
