# Anthropic Agent Patterns Reference

Detailed patterns and findings from Anthropic's published research on building effective agents. Use this as a deep reference when designing complex agents.

Sources:
- *Building effective agents* (Anthropic Research, Dec 2024)
- *Building agents with the Claude Agent SDK* (Anthropic Engineering)
- *How we built our multi-agent research system* (Anthropic Engineering)
- *Effective harnesses for long-running agents* (Anthropic Engineering)
- *Demystifying evals for AI agents* (Anthropic Engineering)

## The Augmented LLM

Every agent starts with an LLM enhanced with three capabilities:

1. **Retrieval**: The model generates search queries to find relevant information
2. **Tools**: The model selects and invokes appropriate tools (APIs, functions, scripts)
3. **Memory**: The model determines what information to retain across interactions

Tailor these capabilities to the specific use case. Ensure each provides a well-documented, easy-to-use interface.

## Workflows vs. Agents

Anthropic draws a firm distinction:

| | Workflows | Agents |
|---|---|---|
| **Control** | Predefined code paths orchestrate LLMs and tools | LLM dynamically directs its own processes and tool usage |
| **Predictability** | High | Lower |
| **Best for** | Well-defined tasks needing consistency | Open-ended problems requiring flexibility |
| **Cost/Latency** | Lower | Higher |
| **Error profile** | Bounded | Compounding |

**Rule of thumb:** Use workflows for well-defined tasks. Use agents for open-ended problems. Only trade the predictability of workflows for the flexibility of agents when the task demands it.

## Five Composable Workflow Patterns

### 1. Prompt Chaining

Decompose a task into sequential steps. Each LLM call processes the output of the previous one. Insert programmatic gates between steps to validate intermediate results.

```
Step A → [validate] → Step B → [validate] → Step C
```

**Best for:** Tasks decomposable into fixed subtasks where earlier outputs inform later steps.

**Example:** Generate marketing copy → translate to target languages → validate tone consistency.

### 2. Routing

Classify inputs and direct them to specialized downstream handlers. Each handler has an optimized prompt for its category.

```
Input → [classify] → Handler A (simple)
                   → Handler B (complex)
                   → Handler C (specialized)
```

**Best for:** Tasks where inputs have distinct categories requiring different treatment.

**Example:** Customer queries routed to billing/technical/general handlers. Simple questions routed to Haiku, complex ones to Opus.

### 3. Parallelization

Two sub-variants:

**Sectioning:** Break a task into independent subtasks that run simultaneously.
```
Input → [Task A] ─┐
      → [Task B] ─┼→ Combine
      → [Task C] ─┘
```

**Voting:** Run the same task multiple times for diverse outputs, then aggregate.
```
Input → [Attempt 1] ─┐
      → [Attempt 2] ─┼→ Vote/Select
      → [Attempt 3] ─┘
```

**Best for:** Speed improvements or gaining confidence through multiple perspectives.

### 4. Orchestrator-Workers

A central LLM dynamically breaks down tasks, delegates to worker LLMs, and synthesizes results. Unlike parallelization, the subtasks are NOT predefined -- the orchestrator determines them at runtime.

```
Input → [Orchestrator] → Worker A (determined at runtime)
                       → Worker B (determined at runtime)
                       → [Synthesize]
```

**Best for:** Complex tasks where subtask requirements cannot be predicted in advance.

**Example:** Multi-file code changes where the orchestrator identifies which files need modification.

### 5. Evaluator-Optimizer

One LLM generates responses; another evaluates and provides feedback in a loop.

```
[Generate] → [Evaluate] → feedback → [Refine] → [Evaluate] → done
```

**Best for:** Tasks with clear evaluation criteria where iterative refinement improves output.

**Example:** Literary translation, complex code generation, multi-round research synthesis.

## The Autonomous Agent Loop

When no workflow pattern suffices, the full autonomous agent is a simple while-loop:

```
while not done:
    gather_context()
    take_action()
    verify_result()
    if satisfied: done = true
```

**When to use:** Open-ended problems with unpredictable step counts where hardcoding paths is impossible.

**Risks:** Higher costs and compounding error potential. Requires sandboxed testing and guardrails.

## Tool Design: The Agent-Computer Interface

Anthropic found they spent **more time optimizing tools than prompts** when building their SWE-bench agent. The Agent-Computer Interface (ACI) deserves the same engineering care as a Human-Computer Interface (HCI).

### Principles

1. **Poka-yoke (error-proof)**: Redesign arguments to make mistakes structurally impossible
   - Example: Requiring absolute file paths eliminated all path-related errors
   - Example: Using file rewrites instead of diffs eliminated line-count errors

2. **Format selection**:
   - Allow sufficient tokens for "thinking" before the model writes structured output
   - Keep formats close to naturally-occurring text
   - Eliminate formatting overhead (line counts, excessive escaping)

3. **Documentation**: Include example usage, edge cases, input format requirements, and clear boundaries with other tools. Test: "Would a junior developer need clarification?"

4. **Non-overlapping**: Each tool should have a clear, unique purpose. Similar tools need extra clarity to differentiate.

5. **Iterative testing**: Run many example inputs, identify where the model makes mistakes, and redesign based on actual usage patterns.

## Multi-Agent System Design

### Results from Anthropic's Research System

- A multi-agent system (Opus lead + Sonnet sub-agents) outperformed single-agent Opus by 90.2% on internal research evals
- Parallel execution cut research time by up to 90% for complex queries
- Token usage alone explains 80% of performance variance; tool calls and model choice explain the remaining 15%

### Delegation Specificity

Vague delegation leads to duplicated work and misinterpretation.

**Bad:** "Research the semiconductor shortage"
**Good:** "Find the top 5 semiconductor manufacturers by revenue in 2024, their reported supply chain disruptions, and any public commitments to capacity expansion. Return as a markdown table with columns: Company, Revenue, Disruptions, Expansion Plans."

### Effort Scaling Heuristics

| Task Type | Sub-agents | Tool Calls Each |
|-----------|-----------|-----------------|
| Simple fact-finding | 1 | 3-10 |
| Direct comparison | 2-4 | 10-15 |
| Broad research | 5-10+ | 15+ |

## Context Management for Long-Running Agents

### Strategies

1. **Agentic search**: Use the file system as an organizational layer. Agents locate and load relevant information without pulling entire documents into context.

2. **Compaction**: Summarize previous messages when context limits approach.

3. **External memory**: For long tasks, maintain:
   - A progress file (chronological log of actions and decisions)
   - Git commit history with descriptive messages
   - Structured task lists

4. **Session handoff**: Clear, structured artifacts (JSON, git history, progress logs) allow agents to understand prior work faster than context compaction alone.

## Verification Strategies

### Rule-Based Validation

Define explicit rules for expected outputs. Code linting and type checking are examples -- they provide automated, deterministic feedback loops.

```markdown
After generating code:
1. Run `npm run lint` and fix any issues
2. Run `npm run typecheck` and resolve errors
3. Run `npm test` and ensure all tests pass
```

### Output Review

Have the agent re-examine its own output against stated criteria.

```markdown
Before presenting results, verify:
- Every finding includes a file path reference
- Severity levels are applied consistently
- No duplicate findings
```

### LLM-as-Judge

A separate model evaluates agent outputs. Less robust than rule-based validation but useful for subjective criteria.

```markdown
Task quality-reviewer("Evaluate this analysis for completeness, accuracy, and actionability")
```

## Evaluating Agents (Evals)

Anthropic's eval roadmap:

1. **Start early** with 20-50 tasks from real failures
2. **Write unambiguous tasks** with reference solutions (two experts should agree on pass/fail)
3. **Build balanced problem sets** (test where behaviors should AND should not occur)
4. **Isolate trials** with clean starting states
5. **Grade outcomes, not paths** -- agents find valid unanticipated approaches
6. **Check transcripts** to verify graders work properly
7. **Monitor for saturation** -- at 100% pass rate, the eval only tracks regressions
8. **Maintain evals** like unit tests with continuous iteration

## Safety and Guardrails

### Multi-Layered Defense

- Run content screening in parallel with the main task (parallelization pattern)
- Use retrieval to ground answers in vetted data
- Enforce tightly-scoped tool access
- Apply least-privilege: start from deny-all, allowlist only needed capabilities

### Human Oversight Tiers

| Risk Level | Oversight |
|-----------|-----------|
| Low (read-only analysis) | Fully autonomous |
| Medium (file modifications) | Report before acting |
| High (deployments, data changes) | Mandatory human approval |

### The Three Core Principles

Anthropic distills everything into:

1. **Simplicity**: Maintain straightforward agent design. Start with the least complex solution.
2. **Transparency**: Explicitly show the agent's planning steps. Make reasoning visible.
3. **Careful ACI Design**: Invest heavily in tool documentation, testing, and error-proofing.
