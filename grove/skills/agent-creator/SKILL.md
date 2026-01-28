---
name: agent-creator
description: This skill provides expert guidance for creating effective Claude Code agents based on Anthropic's research on building agents. It covers agent design, tool interfaces, workflow patterns, multi-agent architectures, and guardrails. Triggers on "create agent", "new agent", "build agent", "agent design".
---

# Agent Creator

Expert guidance for creating effective Claude Code agents, grounded in Anthropic's published research on what makes agents work well.

## Core Principle

> "The most successful agent implementations use simple, composable patterns -- not complex frameworks." -- Anthropic, *Building effective agents*

Start with the simplest solution that works. Only add complexity when simpler approaches demonstrably fail. Measure at each level before escalating.

## Quick Start

Agents are markdown files in `agents/<category>/` that define specialized personas invoked via `Task agent-name(arguments)`.

**Minimal template:**

```markdown
---
name: my-agent
description: "Use this agent when [conditions].\n\n<example>\nContext: [scenario]\nuser: \"[request]\"\nassistant: \"I'll use my-agent to [action]\"\n<commentary>\n[Why this agent is appropriate]\n</commentary>\n</example>"
model: inherit
---

You are [role description].

Your approach:

1. **[First Area]**: [Description]
   - [Sub-point]
   - [Sub-point]

2. **[Second Area]**: [Description]
   - [Sub-point]

When working, provide:
- Clear analysis
- Specific recommendations
- Examples when helpful
```

## Required: YAML Frontmatter

Every agent MUST start with YAML frontmatter:

```yaml
---
name: agent-name
description: "Use this agent when [conditions].\n\n<example>\nContext: [scenario]\nuser: \"[request]\"\nassistant: \"I'll use agent-name to [action]\"\n<commentary>\n[Why this agent is appropriate]\n</commentary>\n</example>"
model: inherit
---
```

**Field requirements:**
- `name`: Lowercase letters and hyphens only
- `description`: Must include `<example>` tags with `<commentary>` showing when and why to use the agent
- `model`: Use `inherit` to match conversation model, or `haiku` for simple/fast tasks

## Agent Categories

Place agents in the appropriate subdirectory:

| Category | Path | Purpose | Examples |
|----------|------|---------|----------|
| Research | `agents/research/` | Information gathering and analysis | Codebase explorer, API researcher, dependency auditor |
| Review | `agents/review/` | Code review and quality checks | Code reviewer, security auditor, accessibility checker |
| Workflow | `agents/workflow/` | Process automation | Release manager, migration assistant, test generator |

Choose the category that best matches the agent's primary function. If unclear, prefer `workflow/`.

## Agent Design Principles

These principles are drawn from Anthropic's research on what makes agents effective.

### 1. Give the Agent a Clear, Focused Role

An agent should do one thing well. A tightly-scoped agent outperforms a broadly-scoped one.

**Good:** "You are a security auditor focused on OWASP Top 10 vulnerabilities."
**Bad:** "You are a helpful assistant that can review code, write tests, and deploy."

Define the role in the first line after the frontmatter. Everything that follows should serve that role.

### 2. Structure the Approach as a Loop

Effective agents follow a gather-act-verify loop rather than a linear sequence:

```markdown
Your approach:

1. **Gather Context**: Understand the problem space
   - Search for relevant code and documentation
   - Identify the scope of what you're working with

2. **Take Action**: Do the core work
   - [Domain-specific steps]

3. **Verify Results**: Check your own work
   - Confirm outputs meet the stated criteria
   - Run any applicable checks
```

This mirrors Anthropic's finding that autonomous agents work best as a loop: gather context, take action, verify, repeat.

### 3. Design the Tool Interface Carefully

Anthropic found they spent more time optimizing tools than prompts. The Agent-Computer Interface (ACI) deserves the same care as a Human-Computer Interface.

**Guidelines for tool usage in agents:**
- Specify which tools the agent should use and when
- Make tool instructions error-proof (prefer absolute paths, explicit formats)
- Each tool reference should be non-overlapping and purpose-specific
- Document expected inputs and outputs

```markdown
## Tools

Use these tools for your work:
- **Grep**: Search for patterns across the codebase
- **Read**: Examine specific files in detail
- **Glob**: Find files by name pattern

Do NOT use Bash for file searching. Use the dedicated search tools.
```

### 4. Include Verification Steps

Agents should check their own work. Anthropic identifies three verification strategies:

- **Rule-based validation**: Run linters, type checkers, tests
- **Output review**: Re-read generated output to confirm correctness
- **Criteria checking**: Verify each stated requirement is met

```markdown
## Verification

Before reporting results:
- [ ] All identified issues include file path and line number
- [ ] Each recommendation is actionable
- [ ] No false positives from pattern matching
```

### 5. Define Clear Output Format

Tell the agent exactly how to present results. Ambiguous output instructions lead to inconsistent agent behavior.

```markdown
## Output Format

Present findings as:

### Summary
[1-2 sentence overview]

### Findings
For each finding:
- **Location**: `file:line`
- **Severity**: Critical / Warning / Info
- **Issue**: What's wrong
- **Fix**: How to resolve it

### Recommendations
[Prioritized list of next steps]
```

### 6. Set Boundaries and Constraints

Explicitly state what the agent should NOT do. Unbounded agents produce worse results than constrained ones.

```markdown
## Constraints

- Do not modify any files; this is a read-only analysis
- Focus only on the specified directory, not the entire codebase
- Report at most 10 findings, prioritized by severity
- If you cannot determine severity, default to Warning
```

## Workflow Patterns

When designing agents, choose the simplest pattern that fits. These are ordered from simplest to most complex.

### Pattern 1: Single-Pass Analysis

The agent examines input and produces output in one pass. No iteration.

**Best for:** Code review, documentation generation, static analysis.

```markdown
You are a [reviewer/analyzer].

1. Read the specified files
2. Analyze for [criteria]
3. Report findings in [format]
```

### Pattern 2: Gather-Then-Act

The agent first collects information, then produces a result based on what it found.

**Best for:** Research tasks, implementation with context, migration planning.

```markdown
You are a [researcher/implementer].

1. **Research Phase**
   - Search for [relevant context]
   - Read [key files]
   - Identify [patterns/requirements]

2. **Action Phase**
   - Based on findings, [produce output]
   - Follow the patterns identified in research
```

### Pattern 3: Iterative Refinement

The agent produces output, evaluates it, and improves it in a loop.

**Best for:** Complex generation tasks, optimization, tasks with clear quality criteria.

```markdown
You are a [generator/optimizer].

1. Produce initial [output]
2. Evaluate against [criteria]
3. If criteria not met, refine and repeat
4. Present final result with explanation of iterations
```

### Pattern 4: Orchestrator-Workers

A lead agent breaks down work and delegates to sub-agents. Use this sparingly -- only when the task truly requires parallel specialized work.

**Best for:** Large-scale analysis, multi-file changes, complex research across many sources.

```markdown
You are a [lead/coordinator].

1. Break the task into independent subtasks
2. Delegate each subtask:
   - Task specialist-a("subtask description")
   - Task specialist-b("subtask description")
3. Synthesize results into a unified output
```

## Multi-Agent Design

When a single agent isn't enough, design a multi-agent system carefully.

### Delegation Best Practices

Each sub-agent needs:
- A **clear objective** (not "research X" but "find all API endpoints that handle authentication and list their HTTP methods and paths")
- An **output format** specification
- **Guidance on tools and sources** to use
- **Defined task boundaries** (what's in scope and out of scope)

### Effort Scaling

Match agent complexity to task complexity:

| Task Complexity | Agents | Tool Calls Per Agent |
|----------------|--------|---------------------|
| Simple fact-finding | 1 | 3-10 |
| Focused comparison | 2-4 sub-agents | 10-15 each |
| Broad research | 5+ sub-agents | 15+ each |

### Context Isolation

Sub-agents are valuable for context management. Each sub-agent uses its own context window and returns only relevant findings, keeping the orchestrator's context clean.

## Guardrails and Safety

### Scope Restrictions

Always define what the agent CAN and CANNOT do:

```markdown
## Permissions

This agent may:
- Read any file in the repository
- Run test commands

This agent must NOT:
- Modify source files
- Run deployment commands
- Access external services
```

### Human Checkpoints

For high-impact agents, build in pause points:

```markdown
After analysis, present findings and wait for user approval before:
- Making any file modifications
- Running any destructive commands
- Proceeding with irreversible actions
```

### Least Privilege

Start with no permissions and add only what's needed. An agent that only needs to read code should not have write access.

## Anti-Patterns to Avoid

- **Kitchen-sink agent**: Trying to handle too many unrelated tasks in one agent
- **Vague role**: "You are a helpful agent" gives no useful direction
- **Missing examples**: Descriptions without `<example>` tags leave Claude guessing about when to use the agent
- **No verification**: Agent produces output with no self-checking step
- **Unbounded scope**: No constraints on what the agent examines or modifies
- **Over-engineering**: Using orchestrator-workers when a single-pass agent would suffice
- **Tool sprawl**: Listing many tools without guidance on when to use each one

## Creating the Agent

1. Choose the appropriate category (`research/`, `review/`, `workflow/`)
2. Create file at `agents/<category>/<name>.md`
3. Write YAML frontmatter with `<example>` tags
4. Define a clear, focused role in the opening line
5. Structure the approach using the simplest suitable pattern
6. Add verification steps and output format
7. Set boundaries and constraints
8. Bump version in `.claude-plugin/plugin.json`

## Success Criteria

A well-designed agent:
- [ ] Has complete YAML frontmatter with `<example>` tags
- [ ] Defines a single, focused role
- [ ] Uses the simplest workflow pattern that fits
- [ ] Specifies which tools to use and when
- [ ] Includes verification/self-checking steps
- [ ] Defines clear output format
- [ ] Sets explicit boundaries and constraints
- [ ] Works with `model: inherit` (or justifies `haiku`)

For detailed workflow patterns and Anthropic's research findings, see:
- [references/anthropic-agent-patterns.md](references/anthropic-agent-patterns.md) - Workflow patterns, multi-agent design, and tool interface guidance from Anthropic's published research
