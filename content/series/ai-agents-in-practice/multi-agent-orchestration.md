---
title: "Multi-Agent Orchestration Multiplies Nondeterminism"
subtitle: "Why Autonomous Coding Systems Fail"
date: 2025-10-18
lastmod: 2025-10-18
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "multi-agent", "orchestration"]
description: "Why adding more agents makes problems exponentially worse, not better."
draft: true
---

After understanding AI agents' fundamental limitations - their nondeterminism, their tendency to generate plausible but incorrect outputs, their inability to maintain system boundaries - I thought I could work around these constraints.

I was wrong.

I tried to build a system where multiple AI agents could work in parallel on different features, coordinated by another AI agent managing the workflow. The theory was sound. The implementation was a disaster. And the lesson is more important than any previous failure mode I've documented.

**Adding more AI agents doesn't solve AI agent problems. It multiplies them exponentially.**

## The Setup: A Reasonable Automation Attempt

I had been using Claude Code successfully for single features:
- Create separate git worktree for feature branch
- Agent works on isolated codebase  
- I verify changes manually, do code review
- Commit if acceptable, discard if not

This worked because I maintained deterministic control:
- I created worktrees
- I assigned tasks
- I verified outputs
- I made commit decisions

The workflow was: **nondeterministic generation → deterministic verification → deterministic integration**

Then I had what seemed like a brilliant idea: **automate the orchestration.**

Someone had already built a harness for multi-agent orchestration. The system would:
- Take high-level feature description
- Decompose into subtasks (via AI agent)
- Determine which tasks can run in parallel (via AI agent)  
- Create worktrees for each parallel task (via AI agent)
- Assign subtasks to worker agents (1-4 agents in parallel)
- Coordinate execution according to plan (via orchestration agent)
- Integrate results when complete

This would let me work on multiple features simultaneously, managed by AI agents while I did other things.

Theory: **nondeterministic orchestration of nondeterministic work producing deterministic result**

Reality: **compounded nondeterminism producing chaos**

## What Actually Happened: Nondeterminism Compounds

Runs took 30 minutes to 2 hours. At the end, I had new changes implementing ~98% of desired functionality and ~2% of something that made the entire exercise pointless.

**Worktree creation nondeterminism:**
- Sometimes created new worktree correctly
- Sometimes worked directly in main branch (dangerous!)
- Sometimes created worktree but also modified main
- No pattern to which behavior I'd get

**Split-brain state:**
- Some worker agents working in new worktree (correct)
- Some worker agents writing to main repo (breaks isolation)
- Orchestration agent confused why bookkeeping inconsistent
- Different tracking files showing same task as "100% complete" and "not started"

**The DRY violation:**
Same information (task status) written in multiple places, getting out of sync because different agents updated different files.

This is basic software engineering: don't duplicate state. But the multi-agent system did it anyway because each agent made independent decisions about where to write status.

## Why This Failed: Nondeterminism Multiplies

Each decision point in the system is nondeterministic:

**Orchestration agent decides:**
- How to decompose feature (nondeterministic)
- Which tasks can parallelize (nondeterministic)  
- Where to create worktrees (nondeterministic)
- When to start each worker (nondeterministic)

**Each worker agent decides:**
- Where to write files (nondeterministic)
- What approach to take (nondeterministic)
- When task is complete (nondeterministic)
- Where to update status (nondeterministic)

**Orchestration agent again decides:**
- How to interpret worker status (nondeterministic)
- When to proceed to next phase (nondeterministic)
- How to handle conflicts (nondeterministic)

With 4 worker agents + 1 orchestration agent, each making multiple nondeterministic decisions, the probability of getting exactly the behavior you want approaches zero.

**Single agent with 10 decision points:** Might work 50% of the time if each decision is 93% reliable

**Five agents with 10 decision points each:** (0.93^50) = ~2% reliability

The nondeterminism doesn't add. It multiplies.

## The Tweaking Death Spiral

I spent significant time trying to fix this:
- Modified orchestration prompts to be more explicit about worktrees
- Changed worker agent instructions to clarify file locations
- Adjusted task decomposition to reduce coordination overhead
- Rewrote status tracking to use single source of truth

**Results:** Sometimes better, sometimes worse, never reliable.

**Because:** Each "fix" was changing probability distributions, not creating deterministic behavior. I was tweaking nondeterministic systems hoping for deterministic outcomes.

At some point I realized: I had modified almost every file in the harness. Very little of the original code remained untouched.

**I had recreated the system to make it work - and it still didn't work reliably.**

## What I Learned (The Hard Way)

**Multi-agent systems amplify every problem of single-agent systems:**

**Plausible bullshit:** Now from multiple agents simultaneously, with conflicts between their lies

**Topology violations:** Multiple agents violating boundaries in different ways at the same time

**Command failures:** Different agents using different wrong commands for same operations

**Nondeterminism:** Each agent's probabilistic decisions compound into system-wide chaos

**Plus new emergent failures:**

**State synchronization:** Agents writing conflicting status to different locations

**Race conditions:** Agents making decisions based on stale state from other agents

**Deadlocks:** Orchestration agent waiting for worker status that never arrives in expected format

**Coordination failures:** Parallel tasks that shouldn't have been parallel because decomposition was wrong

## The Fundamental Problem: No Deterministic Foundation

The multi-agent harness tried to build deterministic orchestration on nondeterministic decisions:
- Decompose feature deterministically from nondeterministic task analysis
- Execute in parallel deterministically from nondeterministic parallelization decisions  
- Integrate results deterministically from nondeterministic completion criteria

**This cannot work.**

You cannot build reliable systems by composing unreliable components without deterministic error correction between layers.

Traditional distributed systems solve this with:
- Deterministic protocols (TCP guarantees, consensus algorithms)
- Explicit error handling (retries, rollbacks, compensation)
- State synchronization (distributed locks, transactions)

Multi-agent AI systems have none of this. Each agent is a nondeterministic black box making independent probabilistic decisions.

## What Actually Works: Human-Verified Checkpoints

The original single-agent workflow succeeded because it had deterministic checkpoints:

1. **I** create worktree (deterministic)
2. Agent works on feature (nondeterministic)  
3. **I** verify changes (deterministic)
4. **I** commit or discard (deterministic)

The nondeterministic generation is sandboxed between deterministic setup and deterministic verification.

**Multi-agent orchestration eliminates these checkpoints.** The system runs autonomously for 30-120 minutes making hundreds of nondeterministic decisions with no deterministic verification until the end.

By then, the probability that everything went right is essentially zero.

## Why This Approach Is Wrong (At Current AI Technology State)

The multi-agent orchestration vision assumes AI agents are components you can compose like microservices or Unix tools:
- Reliable units with clear interfaces
- Deterministic behavior given same inputs  
- Composable into larger reliable systems

**AI agents are none of these things.**

They are nondeterministic probability samplers that sometimes produce useful outputs and sometimes don't, with no way to predict which you'll get.

Trying to build reliable orchestration from unreliable nondeterministic components is fundamentally mismatched to the technology.

## The Correct Pattern: Minimal Automation, Maximal Verification

What works with current AI technology:

**Automate the generation:**
- Use agents to write code
- Use agents to suggest approaches
- Use agents to generate options

**Verify deterministically:**
- Human reviews agent output before accepting
- Automated tests verify correctness  
- Clear checkpoints between agent decisions

**Integrate manually:**
- Human decides what to commit
- Human resolves conflicts
- Human maintains system state

This is slower than autonomous multi-agent orchestration. But it actually works because the nondeterministic generation is contained between deterministic verification points.

## The Hard Truth About Current AI Technology

Multi-agent autonomous systems sound impressive. They promise to automate entire development workflows while you work on other things.

**They don't work reliably with current AI technology** because:
- Nondeterminism compounds across agents
- No deterministic foundation to build on
- Verification only at the end means accumulated errors
- Debugging distributed nondeterministic failures is impossible

You can make them work sometimes. With enough tweaking, careful prompt engineering, and luck, you might get a good run.

But "works sometimes" is not production-ready. It's an experiment that teaches you why the approach is wrong.

## What I Got From This Expensive Lesson

**Not:**
- A working multi-agent orchestration system
- Faster development workflow  
- Autonomous feature development

**But:**
- Deep understanding of how nondeterminism compounds
- Experience with git worktrees and parallel development
- Practice in precise instruction formulation
- Proof that some approaches don't work with current technology

The most valuable lesson: **Just because you can chain AI agents doesn't mean you should.**

Current AI agents work best as individual tools with human oversight, not as autonomous systems with agent oversight.

## Why I'm Documenting This Now

As teams adopt AI agents, some will try multi-agent orchestration. It sounds compelling: automate more, verify less, ship faster.

The ones who understand that nondeterminism compounds will avoid this trap. The ones who don't will spend weeks building increasingly complex orchestration systems that work unreliably.

I'm documenting why this doesn't work so you can skip the expensive lesson I learned.

## The Moral of the Story

AI coding agents are powerful tools for generating options under human supervision.

They are not reliable components for building autonomous systems.

Multi-agent orchestration multiplies nondeterminism, creating systems that fail in unpredictable ways at unpredictable times.

The current state of AI technology requires human checkpoints between agent decisions. This is slower than autonomous operation but actually works.

Don't try to eliminate human oversight. Try to make human oversight efficient.

---
