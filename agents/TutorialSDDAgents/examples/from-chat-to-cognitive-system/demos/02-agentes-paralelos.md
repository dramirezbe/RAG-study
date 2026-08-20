# Parallel sub-agents

> You're going to see how an orchestrator delegates tasks to simultaneous sub-agents to avoid context accumulation.

## Prerequisites

- Claude Code with the delegation rules configured in `CLAUDE.md`
- The `stream-web` project cloned (or any project with differentiated JS and CSS files)

## Context

An agent that does everything alone accumulates tokens with every file it reads and every analysis it does. Eventually it hits the context limit, compaction triggers, and it loses state. The orchestrator/sub-agent pattern solves this: the orchestrator stays lean (it only coordinates), and each sub-agent works with fresh, isolated context. When it finishes, it returns only the summary. The heavy tokens die with the sub-agent.

## Exercise

### Step 1: Ask for two independent investigations

```prompt
I need two independent investigations:
1. Investigate how this project's JavaScript is structured — what app.js does, how it handles navigation and events
2. Investigate how the CSS styles are organized — what design system it uses, variables, breakpoints, and class structure
```

Notice that the orchestrator doesn't investigate itself. It launches TWO sub-agents in parallel because they're completely different domains: JS on one side, CSS on the other. There's no dependency between the findings.

### Step 2: Observe the parallel execution

While they run, you'll see two `Task` blocks running at the same time. Each one:
- Has fresh context (doesn't drag tokens from the other)
- Reads only the files it needs (one reads `.js`, the other reads `.css`)
- Returns a concise summary

The orchestrator is lean: it doesn't read code, it doesn't write code. It only coordinates.

### Step 3: See the synthesis

When both sub-agents finish, the orchestrator combines the results into a unified response. Notice that no context was lost from the main thread, each agent worked with its own scope, and the final result is coherent.

### Step 4: Repeat with another pair of topics

```prompt
Now investigate which AI agents Engram supports and what phases the SDD workflow has
```

Again, two completely unrelated topics. You'll see the same pattern: the orchestrator launches two agents, each one searches its topic, and then synthesizes. If it did everything inline, every file it reads would be tokens accumulating in the main context.

## What happened?

The orchestrator/sub-agent pattern avoids the context accumulation that causes compaction and state loss. Each agent works in isolation and returns only the summary. The orchestrator stays lightweight and can keep coordinating without degrading. If you need 5 analyses, launch 5 sub-agents. It scales without blowing up the context.

## Food for thought

- In which cases would parallelizing be WORSE than a single agent? Think about tasks where the result of one depends on the result of the other.
- If the orchestrator doesn't read code, how can it know if a sub-agent returned an incorrect or incomplete summary?
