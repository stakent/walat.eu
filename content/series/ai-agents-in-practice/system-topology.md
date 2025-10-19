---
title: "AI Agents Can't Maintain System Topology"
subtitle: "Why Your Development Workflow Will Break"
date: 2025-10-18
lastmod: 2025-10-18
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "system-topology", "workflow"]
description: "How agents violate boundaries between edit/sync/execute in distributed development."
draft: true
---

While building personal projects using AI coding agents, I discovered a failure mode that's more subtle and more dangerous than generating plausible bullshit: **agents cannot reliably maintain system topology across their operations.**

This isn't about coding mistakes. It's about agents fundamentally failing to understand where things are, where they should work, and how components of your development infrastructure relate to each other.

Here's what happened, and why it matters for anyone deploying AI agents in production-ready workflows.

## The Setup: A Reasonable Development Workflow

I work across two machines in my local network:

- **Laptop:** Less powerful, where I do editing and review
- **Desktop:** More resources, has GPU, where code executes and tests run

My workflow was designed for efficiency:

1. Edit files in the repository on laptop (source of truth, local, immediate feedback)
2. Background script watches for changes and syncs them to desktop automatically
3. Run code and tests on desktop via SSH (where the resources are)
4. Laptop repo remains the canonical source throughout

This is a simple, clear separation of concerns: **edit here, execute there**, with automatic sync in between.

I documented this in `claude.md`. I specified it at the start of every conversation with Claude Code. I reminded the agent when it seemed confused.

The agent could not maintain this boundary.

## What Actually Happened: Unpredictable Topology Violations

Claude Code would randomly switch between incompatible behaviors:

**Sometimes (correct):**
- Edit files in laptop repo
- SSH to desktop to run tests
- Respect the edit/sync/execute boundary

**Sometimes (breaks source of truth):**
- SSH to desktop
- Edit files in the *desktop* repo directly
- Now I have divergent state across two repositories

**Sometimes (wrong machine):**
- Run tests on laptop instead of desktop
- No GPU, fewer resources, slower execution
- Defeats entire purpose of the split

**Always (the real problem):**
- Completely unpredictable which behavior I'd get
- Required constant monitoring to catch violations
- No way to make the boundary stick across sessions

## Why This Is More Dangerous Than Code Bugs

A coding mistake is local and fixable. Topology violations corrupt your entire development process:

**Loss of source of truth:** When agent edits desktop repo directly, which version is canonical? Laptop? Desktop? The sync script can't resolve conflicts it wasn't designed to handle.

**State divergence:** Changes made on wrong machine don't propagate correctly. You end up with different code in different places, and discovering this costs hours.

**Resource misallocation:** Running tests on laptop instead of desktop means slower feedback loops and wasted time, but worse - you don't know if failures are because code is wrong or because you're testing on the wrong machine.

**Workflow breakdown:** The entire automation (edit → sync → execute) depends on agents respecting boundaries. When they don't, you're back to manual coordination, which defeats the purpose of using agents.

## The Fundamental Problem: Agents Don't Model System Topology

Claude Code doesn't maintain a mental model of your infrastructure. It sees:
- A repository on laptop
- A repository on desktop  
- SSH as a tool it can use

It doesn't understand:
- These are synced copies with one canonical source
- Edits belong on laptop, execution on desktop
- The sync boundary has semantic meaning
- Violating this boundary breaks the workflow

Each time the agent makes a decision, it's evaluating "what seems reasonable right now" without context of:
- What it did last time
- What the system architecture requires
- Why the boundary exists
- What breaks if the boundary is violated

The `claude.md` documentation doesn't persist as architectural constraints. It's just text that might influence the current decision, or might not.

## What This Means for Production Systems

If an AI agent can't maintain edit/sync/execute boundaries in a two-machine local setup, consider what breaks in real production infrastructure:

**Microservices with different deployment targets:** Agent deploys service A to environment for service B. State corruption across system boundaries.

**Monorepos with independent build pipelines:** Agent runs build for package X using pipeline for package Y. Builds succeed but artifacts are wrong.

**Databases with replication:** Agent writes to read replica instead of primary. Changes disappear on next sync.

**Multi-region deployments:** Agent applies config to wrong region. Now you have state divergence across geographies.

**Secrets management:** Agent reads secrets from wrong environment and uses them in another. Security boundary violated.

In every case, the agent didn't "make a mistake" - it violated system topology because it doesn't maintain a model of how components relate.

## The Operational Pattern: Constant Vigilance

There's no elegant solution. The patterns that work are expensive:

**Monitor every agent action:** Don't trust the agent understood your topology. Verify every file edit, every command execution, every environment access. Check it touched the right system.

**Single-system constraints:** Simplify topology to reduce violation surface. If agent only has access to one machine, it can't edit the wrong repo. But this defeats distributed development benefits.

**Explicit verification after each step:** Agent claims it edited laptop repo and ran tests on desktop? Verify both claims independently. Check file timestamps, check process logs, confirm state matches claims.

**Session boundaries reset context:** Never assume agent remembers topology across conversations. Re-explain the architecture every session, even though you documented it in `claude.md`.

**Fail-fast on violations:** The moment you catch agent working on wrong machine, stop everything. Fix state divergence before proceeding. Don't let violations compound.

## The Productivity Trade-off

This monitoring overhead is not optional if your workflow has any system boundaries:

- Time saved by agent writing code
- Minus time spent verifying it worked on correct system
- Minus time recovering from topology violations
- Minus time re-synchronizing divergent state

The net productivity gain shrinks significantly. You're still faster than without AI, but the speedup comes with mandatory infrastructure babysitting.

## Why I'm Documenting This Now

As AI agents become standard in development workflows, teams will hit these topology failures. The ones who've worked out monitoring and recovery patterns will ship reliably. The ones who trust agents to "understand the architecture" will spend weeks debugging state corruption.

The failure mode is predictable:
1. Team adopts AI agents for productivity
2. Agents violate system boundaries unpredictably  
3. State diverges across environments
4. Debugging takes longer than agent saved
5. Team either abandons agents or develops expensive monitoring

I'm documenting step 5 so you can skip steps 3 and 4.

## The Hard Truth

AI coding agents are powerful tools that cannot maintain system topology. This isn't a bug to be fixed in the next version - it's a fundamental characteristic of how current agents work.

They don't model your infrastructure. They don't remember architectural constraints across sessions. They don't understand why boundaries exist.

Every decision is local and context-free, which means every decision can violate topology that exists outside the agent's immediate context window.

If your development workflow has any system boundaries - and every real workflow does - you need monitoring and recovery procedures, or you will lose days to state corruption.

The companies that understand this will use agents productively within carefully monitored boundaries. The ones that don't will blame the agents when their infrastructure diverges.

---
