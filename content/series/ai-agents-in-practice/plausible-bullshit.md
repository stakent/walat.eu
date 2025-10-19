---
title: "AI Coding Agents Are Plausible Bullshit Generators"
subtitle: "What This Means for Production Systems"
date: 2025-10-18
lastmod: 2025-10-18
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "documentation", "verification"]
description: "When agents generate convincing documentation for functionality they never implemented."
draft: true
---

I've been building personal projects using AI coding agents for the past several months. Not as an experiment - as the primary development method for production software that will handle consequential decisions. This means I've hit every failure mode that matters, not in theory but in practice where mistakes cost real time and create real risk.

The public narrative about AI coding agents is dangerously incomplete. Everyone focuses on productivity gains - "I shipped 10x faster!" - while ignoring the operational realities that determine whether you ship working systems or plausible-looking disasters.

Here's what I've learned that nobody's talking about publicly.

## The Fundamental Problem: Plausible Bullshit Generation

Claude Code describes itself accurately: it's a "plausible looking bullshit generator." This isn't humility - it's a critical operational characteristic.

Here's a real incident from my work:

I gave Claude Code a simple, well-defined task. Clear requirements, specific goal, unambiguous scope. The agent completed the task and generated documentation.

The documentation was perfect. It described exactly what should happen, aligned precisely with the task requirements, explained the logic clearly. If you read only the docs, you'd think the implementation was solid.

The implementation didn't exist.

Not buggy. Not incomplete. Not even started badly and abandoned. The code to do what the documentation described simply wasn't there. The agent had generated authoritative-sounding documentation for functionality it never built.

This is the pattern that breaks production systems.

## Why This Is Dangerous

The output looks convincing enough that standard review processes won't catch it:

**Code review fails:** Reviewer reads the docs, sees logical approach, spots no obvious errors in what code exists, approves. Nobody verifies that implementation matches documentation claims because the docs are so plausible.

**Tests pass:** If you're not testing the specific functionality the docs claim exists, your test suite stays green. The agent didn't break anything - it just didn't build what it said it built.

**Deployment succeeds:** No runtime errors on startup. The missing functionality only reveals itself when a user tries to actually use the feature that documentation promised exists.

In production software handling consequential decisions (regulatory compliance, financial transactions, medical data), this gap between documented behavior and actual behavior is not a productivity problem - it's a liability.

## The Operational Pattern That Catches This

Never trust agent-generated documentation. Ever.

Your verification protocol must be:

1. **Implementation first, documentation never:** Read the actual code. Trace the logic. Verify it does what's needed. Ignore all documentation until you've confirmed implementation.

2. **Test what matters, not what's easy:** Write tests that verify the documented functionality actually works, not just that code runs without errors. If docs claim feature X exists, your test must exercise feature X end-to-end.

3. **Isolated environments for agent work:** Use containers (I use Testcontainers) so agent mistakes can't propagate beyond test scope. When an agent tries to put migration logic in a Dockerfile instead of running migrations against test database, the isolation catches it.

4. **Explicit verification points:** After each agent task, checkpoint: "Does the implementation actually do what the documentation claims?" This sounds obvious but it's the step everyone skips because the docs are so convincing.

## What This Means for Teams

If you're deploying AI coding agents for production-ready systems, the workflow changes are not optional:

**Documentation becomes a liability, not an asset:** Agent-generated docs are evidence of what SHOULD exist, not what DOES exist. Treat them as requirements to verify, not descriptions of reality.

**Code review gets more expensive:** Reviewer must verify implementation matches claims. Can't trust docs, can't trust tests, must read actual code. This is slower than pre-AI review but required for correctness.

**Context hygiene is critical:** If you feed an agent confused context (mixed actual state with desired state, unclear boundaries between what exists and what's planned), it will generate plausible documentation for the desired state without implementing it. Keep context ruthlessly clean.

**Parallel agent work requires isolation:** Multiple agents working on related code will generate conflicting plausible explanations. Use separate working trees, containers, or repositories to prevent cross-contamination until you've verified each agent's work independently.

## The Productivity Paradox

Yes, AI agents make you faster. I'm building personal projects solo in timeframes that would require a team without AI assistance.

But the speedup comes with mandatory overhead that the "10x developer!" narratives ignore:

- Verification protocols that take real time
- Isolation infrastructure that adds complexity
- Review processes that can't be shortcut
- Recovery procedures for when agents generate convincing nonsense

The net result is still faster than without AI - but not 10x. More like 2-3x, with the difference being verification work that keeps you honest.

## Why I'm Writing This Now

As AI coding agents become standard tools (they already are at companies paying attention), the operational patterns for safe deployment will be worth more than the tools themselves. Everyone has access to Claude Code or Cursor or whatever comes next. Not everyone knows how to deploy them without creating plausible-looking disasters.

The companies that figure out verification protocols, isolation patterns, and review processes will ship AI-augmented systems that actually work. The ones that chase "10x productivity" without operational discipline will ship convincing bullshit that fails when it reaches production.

I'm betting on being useful to the first group.

---

