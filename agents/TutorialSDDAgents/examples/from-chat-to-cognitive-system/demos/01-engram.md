# Engram: Persistent memory for agents

> You are going to experience how an AI agent can remember decisions between sessions using Engram.

## Prerequisites

- Claude Code installed and working
- Engram configured (the protocol in your `CLAUDE.md`)
- An open project where you can make architecture decisions

## Context

AI agents are **stateless by default**. Every time you open a new session, the agent starts from zero: it doesn't know what you decided yesterday, what conventions you defined, or what bugs you fixed. It's like working with a dev who has amnesia every day. Engram solves this by adding a persistent memory layer that survives between sessions, without you having to repeat yourself.

## Exercise

### Step 1: Make an architecture decision

Open Claude Code in your project and send this prompt:

```prompt
I want to use Clean Architecture with dependency injection in this project. The layers will be: domain, application, infrastructure, presentation. Let's use a repository pattern for data access.
```

Notice in the output that Claude does a `mem_save` automatically. Nobody asked it to: the Engram protocol triggers it on its own when it detects an architecture decision.

### Step 2: Verify it was saved

```prompt
What architecture decisions do we have on record?
```

You'll see Claude do a `mem_search` and retrieve the decision. Observe the `topic_key` it assigned, something like `architecture/clean-architecture`.

### Step 3: Close the session properly

```prompt
OK, let's close the session
```

Wait for Claude to run the `mem_session_summary` before exiting. Only when it finishes, use `/exit`. If you hit Ctrl+C directly, you kill the process without giving it a chance to save the summary.

### Step 4: Open a new session

Open Claude Code again in the same project:

```bash
claude
```

```prompt
What architecture did we decide to use for this project?
```

You'll see Claude do `mem_context` + `mem_search` and retrieve the Clean Architecture decision with the exact layers. Without Engram, this would have been lost when you closed the terminal.

### Step 5: Verify from the CLI

In another terminal, try the direct search:

```bash
engram search "architecture" --project stream-web
```

Notice that Engram has its own CLI. You can search memories without opening Claude Code. Underneath it's a SQLite database with FTS5 (native full-text search).

## What happened?

Engram turns a stateless agent into a system with persistent memory. Decisions, conventions and discoveries are saved automatically and retrieved in future sessions. It's not magic: it's a protocol that triggers `mem_save` on certain triggers (decisions, bugfixes, discoveries) and `mem_context` at the start of each session.

## Food for thought

- What happens in a project where 3 devs use the same agent but don't share memory? How many architecture decisions get repeated or contradicted?
- If the agent "remembers" a wrong decision, how does that impact all the code it generates afterwards?
