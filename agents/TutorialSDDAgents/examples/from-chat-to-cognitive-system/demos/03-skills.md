# Skills: Precise context on demand

> You're going to see how skills load specific instructions only when needed, instead of a monolithic file that pollutes every session.

## Prerequisites

- The `~/.claude/skills/` folder with at least the `react-19` and `typescript` skills
- Claude Code open in a project

## Context

The naive approach is to put ALL the agent's instructions in a single file: React conventions, TypeScript rules, testing patterns, Tailwind configuration... all together, all the time. That's easily 2400 lines loaded into context BEFORE you write a single prompt. Every instruction token is one less token for your code. Skills solve this: they're independent modules that load only when the context requires it.

## Exercise

### Step 1: See the skills structure

```bash
eza --tree ~/.claude/skills/ --level 1
```

You'll see that each skill is a folder with a `SKILL.md`. React, TypeScript, Tailwind, Zustand, Next.js — each one with its specific rules. They're not all loaded at once.

### Step 2: Trigger a skill automatically

```prompt
Create a React component to display a list of users with name and email. Use TypeScript.
```

Notice in the output: Claude reads `~/.claude/skills/react-19/SKILL.md` and `~/.claude/skills/typescript/SKILL.md` BEFORE writing code. It detected React and TypeScript in the request, loaded both skills automatically, and the code follows the conventions defined there — it's not generic code.

### Step 3: Inspect a skill's rules

```bash
bat ~/.claude/skills/react-19/SKILL.md --line-range 10:50
```

You'll see concrete rules: don't use `useMemo`/`useCallback` because React Compiler handles it, use function declarations, naming conventions. These are YOUR team's rules, not Claude's defaults.

### Step 4: Gauge the difference

Think about the numbers:
- A typical `SKILL.md` is ~80 lines
- A monolithic `AGENTS.md` can have 2400+ lines loaded ALL THE TIME
- With skills, only what's relevant to the current context is loaded
- Fewer instruction tokens = more room for your code = less compaction

It's like the difference between loading the whole encyclopedia vs opening the chapter you need.

## What happened?

Skills are modular, on-demand context. Instead of polluting every session with thousands of lines of instructions, only the rules relevant to the current work are loaded. The agent detects the context (React, TypeScript, Tailwind) and loads the corresponding skills before writing a single line of code. This keeps the context clean and the instructions precise.

## Food for thought

- What if you had skills with your company's specific conventions? How would onboarding change for a new dev who uses agents?
- If a skill has a wrong rule, how much incorrect code can the agent generate before someone notices?
