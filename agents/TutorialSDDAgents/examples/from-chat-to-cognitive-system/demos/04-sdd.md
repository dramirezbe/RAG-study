# Exercise: SDD — From idea to dashboard in minutes

> You're going to use Spec-Driven Development to generate a complete cybersecurity dashboard from scratch. Without writing a single line of code manually.

## Prerequisites

- Claude Code installed and configured
- Agent Teams Lite active (CLAUDE.md with orchestration rules)
- Engram working (for persistence between phases)
- An empty folder for the project (e.g. `mkdir ~/cyber-dashboard && cd ~/cyber-dashboard`)

## Context

"Vibe coding" is throwing a prompt at the agent and praying something decent comes out. Sometimes it works, sometimes it generates a Frankenstein of code you can't maintain or explain. SDD is the opposite: instead of "make me a dashboard", you go through an engineering pipeline — explore, propose, specify, design, break down into tasks, implement, verify. Each phase is executed by a sub-agent with clean context that produces a reviewable artifact. The result isn't "whatever the LLM felt like generating", but code that meets concrete specifications you defined.

## Exercise

### Step 1: Start the change

```prompt
/sdd-new cyber-dashboard
```

When it asks what it's about, explain:

```prompt
I want to create a cybersecurity dashboard in a single HTML file with inline CSS and JS. Visual theme Kanagawa Blur (background #1A1B26, cards #24283B with glassmorphism). It must have: header with title "Security Dashboard" and a green status badge, 4 severity cards (Critical red #F7768E, High orange #FF9E64, Medium yellow #DFBD76, Low blue #7AA2F7) with animated counters, a horizontal bar chart showing vulnerabilities by provider (AWS, GCP, Azure), staggered entrance animations, hover with glow, and the status bar with pulse breathing. Hardcoded but realistic data.
```

You'll see SDD launch an Explorer that analyzes the requirements and then a Proposer that generates a formal proposal with scope, approach and rollback plan. Notice they're two different sub-agents, each with fresh context.

### Step 2: Generate specifications and design

```prompt
/sdd-continue
```

This generates specs in Given/When/Then format — what the dashboard MUST comply with. It's not code, it's verifiable acceptance criteria.

```prompt
/sdd-continue
```

Now the design is generated: technical decisions, file structure, patterns to use. Specs and design can come out in any order because they're parallel in the dependency DAG — that's normal.

### Step 3: Generate tasks

```prompt
/sdd-continue
```

The Task Planner reads the specs + design and generates a concrete execution plan: numbered tasks, ordered, with individual acceptance criteria. Notice each task is small and focused — it's not "do the whole dashboard".

### Step 4: Implement

```prompt
/sdd-apply
```

Only HERE is code written. The Implementer starts with clean context, reads the tasks + specs + design, and generates the HTML/CSS/JS against those artifacts. It doesn't improvise: it implements what was specified.

### Step 5: Verify

```prompt
/sdd-verify
```

The Verifier checks the implementation AGAINST the specs from step 2. It's not "looks fine to me" — it's systematic verification spec by spec. You'll see a report with what passed and what failed.

### Step 6: See the result

```bash
open index.html
```

Open the file in the browser. You'll see the glassmorphism cards appearing with staggered fade, the severity numbers counting up from zero, the chart bars growing left to right, and the green badge breathing with a soft pulse. All in a Kanagawa Blur palette on a dark background.

## What happened?

You generated a complete dashboard with 6 sub-agents, each with clean context, each producing a reviewable artifact. The idea went through exploration, proposal, specification, design, task breakdown, implementation and verification. That's NOT vibe coding — it's engineering with agents.

The key difference: every line of code has a spec behind it. If something doesn't work, you know exactly which spec broke, in which phase it was defined, and where to fix it. When you throw a loose prompt at a chat and pray, you have none of that.

```
idea → explore → proposal → specs + design → tasks → apply → verify
                                                        ↑
                                             each phase is an agent
                                             with clean context
```

## Food for thought

- Which phase would catch most bugs BEFORE they reach production? Why that one and not another?
- If you asked this same dashboard from a chat without SDD, what things could come out different? What would be lost?
