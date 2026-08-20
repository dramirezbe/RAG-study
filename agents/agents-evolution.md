# Evolution of Agentic Architectures

## AGENTS.md: the contextual monolith

It all started with `AGENTS.md`: a giant markdown file with ALL of the project's rules — style, patterns, anti-patterns, conventions, workflows. The agent read it at startup and "knew" how to work.

**Characteristics:**

- **Single load at session start:** The agent consumes the whole file as part of the system prompt or first message.
- **Conceptual simplicity:** A single configuration point. Easy to understand, easy to version.
- **Typical content:** Code style, project structure, build commands, linting rules, naming conventions, frameworks used, repo gotchas.

**Problem:** Every new convention fattened the file. In real projects, `AGENTS.md` can reach thousands of lines. It eats the context window before you write your first prompt. The agent starts with the context tank half empty and pays for it on every turn of the conversation.

---

## AGENTS.md grows infinitely

The underlying problem is not the file itself, but the premise of *total context preloading*: reading EVERYTHING just in case SOME THING is needed.

**Symptoms of the uncontrolled monolith:**

- **Contextual bloat:** 2000+ lines of rules where maybe only 50 are relevant to your task. Tokens wasted on every request.
- **Maintenance friction:** The team avoids documenting new conventions because "it's already too long." The file stagnates, and the gap between what is documented and what is real grows.
- **Ambiguity from saturation:** Too much diffuse information competes for the model's attention. Contradictory rules between sections written months apart.
- **Cumulative cost:** On token-priced models (GPT-4, Claude), every turn pays for re-reading the whole monolith.

---

## First evolution: Skills

Instead of a giant file, a lightweight *router* (`AGENTS.md`) that points to specific `SKILL.md` files depending on the task. Only the context you need for the current task is loaded. Dynamic context on demand.

**How it works:**

- **AGENTS.md becomes an index:** It only contains a table of available skills with their description and location. The agent reads it at startup (minimal cost) and decides which one to load based on the user's task.
- **Each skill is a self-contained `SKILL.md`:** One file per domain with precise instructions, examples, workflows, and references. Loaded *on-demand*: only when the agent detects the task matches.
- **Typical skill structure:**
  - `git/SKILL.md` — Commit flow, branches, PRs, message conventions.
  - `testing/SKILL.md` — Frameworks, commands, minimum coverage, allowed mocks.
  - `db/SKILL.md` — Migrations, ORM, table naming, index rules.
  - `deploy/SKILL.md` — CI/CD, environments, variables, health checks.

**Advantages:**

- **Precise context:** The agent only pays tokens for what it will use. If you're going to write tests, you don't load deploy rules.
- **Maintainability:** Each skill has a clear owner. Adding a new one doesn't touch the others.
- **Horizontal scalability:** The number of skills can grow without degrading contextual efficiency.

**Limitation:** Skills solve WHICH context to load, but not the accumulated noise in the conversation. A single agent handling multiple skills in a long session still accumulates history tokens.

---

## Second evolution: Sub-agents

Skills solve *what* context to load. But a single agent still accumulates noise. The solution: delegate each phase to an ephemeral sub-agent with fresh context. Born, executes, reports, and dies.

**How it works:**

- **Orchestrator + workers:** A main agent (orchestrator) receives the user's task, breaks it into sub-tasks, and launches independent sub-agents for each one.
- **Ephemeral sub-agent:** Each sub-agent receives clean context (sub-task instructions + relevant skill), executes its work, and returns a summary to the orchestrator. Then it disappears — it does not accumulate history.
- **Real parallelism:** Independent sub-agents can run in parallel (e.g., one researches the documentation while another looks for patterns in the code).

**Comparison with the monolithic model:**

| Aspect | Single agent | Sub-agents |
|---|---|---|
| **Accumulated context** | Grows linearly with each action | Each sub-agent starts fresh |
| **Parallelism** | Sequential | Independent tasks in parallel |
| **Specialization** | One agent knows everything | Each sub-agent gets a specific skill |
| **Noise** | Previous decisions contaminate | Isolated, does not see others' decisions |
| **Cost** | Growing tokens per turn | Fixed tokens per sub-task |

**Limitation:** Sub-agents do not share state. If two sub-agents need the same base information, they load it twice. And the orchestrator must be smart enough to decompose and consolidate correctly.

---

## The current pattern: everything together

The architecture that solves LLM limitations with engineering, combining the four elements:

```
Orchestrator (coordinates)
    ├── Skills (precise context on demand)
    ├── Sub-agents (clean and ephemeral execution)
    └── Persistent memory (continuity across sessions)
```

**Components of the current stack:**

### 1. Orchestrator
The main agent that receives the user's task, breaks it down, decides which skills and sub-agents to launch, and consolidates the results. It is the *coordinating brain*. It does not execute heavy tasks directly — it delegates.

### 2. Skills (precise context)
The lightweight router (`AGENTS.md` as index) + on-demand `SKILL.md`. Solves the problem of "what the agent needs to know for this specific task." Context is loaded only when the orchestrator or a sub-agent requests it.

### 3. Sub-agents (clean execution)
Ephemeral workers born with fresh context (instructions + skill), execute a concrete sub-task, report to the orchestrator, and die. They do not accumulate noise from previous conversations. They can run in parallel.

### 4. Persistent memory (continuity)
State files, knowledge bases, or memory banks that survive the agent's session. They allow the orchestrator and sub-agents to remember previous decisions, learn from feedback, and maintain coherence across sessions without reloading the entire history.

**Why it works:**

- **Breaks the context window:** Skills load only what is needed. Sub-agents do not accumulate history. Memory stores what matters outside the prompt.
- **Scales with the project:** More rules = more skills, not more tokens per turn. More complexity = more sub-agents, not more noise.
- **Real parallelism:** Independent tasks run simultaneously instead of sequentially.
- **Predictable cost:** Tokens per task instead of growing tokens per session.

**The fundamental principle:** Do not try to make a single LLM know everything and remember everything. Instead, give it the right tool (skill), the right focus (sub-agent), and the right memory (persistence) for each moment. The intelligence is not in the model — it is in the architecture that surrounds it.