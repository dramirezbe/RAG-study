# Spec-Driven Development (SDD)

## What is Spec-Driven Development?

**Description:** Development methodology where the specification (*spec*) is the primary artifact and source of truth of the project. Instead of writing code directly, you define *what* the system should do in structured natural language, and an AI agent translates that specification into implementation. The spec is not accessory documentation — it is the *input* that governs the entire cycle: code generation, tests, validation, and maintenance.

**Analogy:** Just as TDD (Test-Driven Development) put tests before code, SDD puts the specification before tests. You don't write a single line of code until the spec is clear, validated, and versioned.

---

## The SDD cycle

```
SPEC → PLAN → GENERATE → VALIDATE → ITERATE
  ↑                                    │
  └────────────────────────────────────┘
```

### 1. SPEC
You write the specification in structured natural language. Define:
- **Entities and models:** What data the system handles.
- **Behaviors:** What actions it can perform (endpoints, functions, flows).
- **Constraints:** Business rules, invariants, validations.
- **Edge cases:** What happens under boundary or error conditions.

The spec is written once and versioned in the repository like any other source file.

### 2. PLAN
The agent reads the spec, breaks it down into atomic tasks, and generates an implementation plan. Each task has:
- Precise description of what it must produce.
- Required skills and context.
- Dependencies between tasks.
- Acceptance criteria.

### 3. GENERATE
Ephemeral sub-agents execute each task of the plan:
- One agent generates the file structure and scaffolding.
- Another implements the models and types.
- Another writes the business logic.
- Another generates the tests that validate the spec.
- Another writes migrations, configs, and boilerplate.

Each sub-agent only sees its task and the relevant section of the spec. Minimal context, maximum focus.

### 4. VALIDATE
Post-generation, the validation cycle verifies that the output complies with the spec:
- **Automatic tests:** The tests generated in the previous phase are run. If they fail, the agent iterates.
- **Spec-compliance check:** A reviewer agent compares the implementation against the original spec and reports divergences.
- **Linting and typecheck:** Standard quality gates of the project's stack.
- **Human-in-the-loop (optional):** Human review at critical points (security, architecture decisions).

### 5. ITERATE
Changes to the spec trigger incremental re-generation:
- The agent detects which parts of the code became obsolete.
- Regenerates only what is necessary, preserving the rest.
- Runs regression tests to make sure nothing broke.

---

## Advantages over traditional development with AI

### Architectural consistency
In a free chat with AI, each session can make inconsistent decisions. With SDD, the spec acts as an architectural *constraint*: the agent does not improvise, it follows the specification. Two features implemented in separate sessions maintain coherence because both read the same spec.

### Auditability
The spec is versioned plain text. You can run `git diff` and see exactly what changed in the requirements and how that is reflected in the generated code. The *blame* moves from "the agent decided this" to "the spec says this." Full traceability from requirement to implementation.

### Reproducibility
Given the same spec and the same model, you get the same architecture. You don't depend on a conversation history or accumulated prompts. The spec is deterministic: you can regenerate the entire project from scratch if necessary.

### Real parallelism
Since the spec defines the contract between modules (interfaces, types, responsibilities), multiple sub-agents can implement independent parts in parallel without conflicts. Each one knows the boundaries of its module and the interfaces with the others.

### Agent onboarding
A new agent doesn't need to read 3000 lines of code to understand the project. It reads the spec (200-500 lines), understands the whole system, and can contribute immediately. The spec is the project's *system prompt*.

### Safe refactoring
Before a big refactor, you update the spec. The agent regenerates the affected parts ensuring global consistency. There is no "I touched too much" — the spec defines exactly what should have changed.

---

## When to use SDD

| Scenario | SDD | Traditional approach |
|---|---|---|
| **New project** | Spec from scratch, complete generation | Iterative chat, emergent architecture |
| **Large feature** | Update spec → regenerate module | Ad-hoc prompt, risk of inconsistency |
| **Refactor** | Spec-driven: change the spec first | Explore code → guess impact |
| **AI code review** | Validate spec-compliance | Blind line-by-line review |
| **Ongoing maintenance** | Spec is a living document | The code is the only truth |
| **Quick prototype** | Unnecessary overhead | Direct chat, no spec |

---

## Relationship with the agentic stack

SDD is the natural complement to the pattern described in [agents-evolution.md](agents-evolution.md):

```
SPEC (source of truth)
  │
  ├── Orchestrator: reads the spec, generates the plan
  │     │
  │     ├── Skill: loads the relevant skill according to the module
  │     │
  │     └── Sub-agents: implement plan tasks
  │           │
  │           └── Persistent memory: the spec itself is memory
  │
  └── VALIDATE: spec-compliance check → iterate
```

The spec closes the loop: it is the *input* that governs everything and the *output* against which everything is validated. The agent does not decide *what* to build — the spec decides it. The agent decides *how* to build it.

---

## Current limitations

- **Ambiguity in natural language:** An ambiguous spec produces ambiguous code. The quality of the output depends directly on the precision of the spec. Writing good specs is a skill in itself.
- **Overhead for small tasks:** For a one-line fix, writing a spec is overhead. SDD shines on features, modules, and complete systems, not on micro-changes.
- **Spec-code drift:** If someone modifies the code without updating the spec, the source of truth splits. It requires team discipline or tooling that detects divergences automatically.
- **Creativity limited by the spec:** The agent can only innovate within the margins the spec defines. If the spec does not contemplate an optimization, the agent will not propose it.
