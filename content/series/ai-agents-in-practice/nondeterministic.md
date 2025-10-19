---
title: "AI Agents Are Nondeterministic"
subtitle: "Why This Makes Them Fundamentally Unreliable for Production"
date: 2025-10-18
lastmod: 2025-10-18
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "nondeterminism", "reliability"]
description: "The root cause of most agent failures: probabilistic outputs from deterministic requirements."
draft: true
---

There's a fundamental problem underlying AI agent failures: **AI agents are probabilistic systems producing nondeterministic outputs.**

Given the same prompt, same context, same history - you get different results every time. Sometimes slightly different wording. Sometimes fundamentally different decisions, different approaches, different commands.

This isn't a bug. This is how the technology works. And it makes using AI agents for production workflows risky without proper guardrails and verification - because most production tasks require predictable, deterministic behavior.

## The Core Problem: Same Input, Different Output

When I ask Claude Code to run tests, I might get:
- `uv run pytest` (correct)
- `pytest` (wrong, bypasses environment)  
- `python3 -m pytest` (wrong, bypasses uv)
- `python -m pytest tests/` (wrong in multiple ways)

Same context. Same documentation. Same conversation history. Different command each time.

When I ask it to edit a file:
- Sometimes edits laptop repo (correct)
- Sometimes SSHs to desktop and edits there (breaks source of truth)
- Sometimes asks which machine to use (acknowledging uncertainty)
- Sometimes does something completely different

The output is **nondeterministic**. You cannot predict what you'll get.

This is not a training problem or a prompt engineering problem. This is the fundamental nature of probabilistic language models. Given the same input, they sample from a probability distribution over possible outputs. The sampling produces different results each time.

## Why This Is Incompatible With Production Systems

Production systems require **deterministic behavior**:

**Same input → Same output**

This is the foundation of:
- **Testing:** Run the same test twice, get same result (barring actual code changes)
- **Deployment:** Same deployment command → same infrastructure state
- **Debugging:** Reproduce the problem by repeating the steps
- **Automation:** Scripts that do the same thing every time they run
- **Compliance:** Auditable processes that work consistently

AI agents violate this everywhere:

**Testing:** Ask agent to run test suite, sometimes it runs on laptop, sometimes desktop, sometimes uses wrong Python interpreter.

**Deployment:** Ask agent to deploy to staging, might deploy to staging, might deploy to production, might deploy to wrong region.

**Debugging:** "Reproduce what you did last time" - agent can't, because last time was a probabilistic sample that won't occur again.

**Automation:** Script using AI agents produces different results each run. Not useful for automation.

**Compliance:** "Show that this process works consistently" - can't, because process is nondeterministic.

## The Absurdity: Using Nondeterministic Tools for Deterministic Tasks

We have excellent deterministic tools:

**For calculations:**
- Python can compute exact results
- numpy/scipy for numerical operations
- sympy for symbolic mathematics  
- All tested, all deterministic, all reliable

**For system operations:**
- bash scripts that run identically every time
- Make/Bazel that produce same builds from same inputs
- Terraform that converges to defined state
- All deterministic, all predictable

**AI agents can use these tools.** They can call Python to do math. They can execute bash scripts. They can invoke deterministic systems.

Yet people try to use AI agents directly for tasks requiring deterministic behavior:
- "Calculate this result" - agent hallucinates numbers instead of calling calculator
- "Deploy this configuration" - agent guesses at commands instead of using tested scripts
- "Run these migrations in order" - agent decides order nondeterministically instead of following explicit sequence

**This is using the wrong tool for the job.**

It's like using a random number generator when you need a counter. The technology is fundamentally mismatched to the requirement.

## Why the Previous Failures Are Inevitable

Common AI agent failure modes are all consequences of nondeterminism:

**Plausible bullshit:**
Agent samples from probability distribution over "what documentation should look like." Sometimes samples accurate description of implementation. Sometimes samples plausible-sounding description of desired functionality. Nondeterministic which you get.

**Topology violations:**
Agent samples from distribution over "how to execute this task." Sometimes samples "edit laptop repo, SSH to desktop for execution." Sometimes samples "SSH to desktop, edit there." Context influences probability but doesn't determine outcome.

**Command pattern failures:**
Agent samples from distribution over "how to run Python tests." Strong prior from training data makes `pytest` high probability. Documentation makes `uv run pytest` somewhat higher probability. But still sampling - sometimes gets one, sometimes other.

All these failures are **sampling variation from nondeterministic process**.

You can't fix this with better prompts or better documentation. The nondeterminism is intrinsic to how the models work.

## The Pattern That Doesn't Work: Expecting Consistency

Teams adopting AI agents often assume:
- "Once it learns the right way, it'll do it consistently"
- "If I document it clearly enough, it'll follow the rules"
- "After I correct it a few times, it'll remember"

None of this is true.

The agent doesn't "learn" from your corrections in a way that makes future behavior deterministic. Your corrections shift probability distributions slightly. But the output is still sampled probabilistically.

Next session, next context, next random seed - different sample, different behavior.

Expecting consistency from a probabilistic system is like expecting a coin flip to "learn" to land heads-up after you tell it to several times.

## The Pattern That Works: Deterministic Verification

Since you can't make agents deterministic, you must verify nondeterministic outputs deterministically:

**Agent outputs command → You verify command is correct → Then execute**

Not: "Agent executes command, I check if it worked"

But: "Agent proposes command, I verify against deterministic rules, then I execute"

The verification must be deterministic:
- Is this command in the approved list?
- Does this command use required tool (`uv run` not `python3`)?
- Will this command affect the correct system (laptop not desktop)?
- Does this match the documented procedure?

Checklist-style verification, not judgment calls.

## Why AI Agents Work for Code Generation

AI agents are actually useful for writing code, despite being nondeterministic. Why?

**Code generation tolerates variation:**
- Multiple correct implementations exist
- Nondeterminism produces different approaches to same problem  
- Testing verifies correctness regardless of approach
- Final code is deterministic even if generation process wasn't

The nondeterministic generation creates a deterministic artifact (code) that you then verify deterministically (tests).

This workflow matches the technology:
1. Use nondeterministic agent to generate options
2. Use deterministic verification to validate
3. Keep the validated artifact

## What Breaks: Using Agents for Execution

Where teams get in trouble:
- Using agents to execute deployments (nondeterministic deployment commands)
- Using agents to run operations (nondeterministic system modifications)
- Using agents to make production decisions (nondeterministic judgment)

In each case, the nondeterminism creates direct risk because there's no deterministic artifact to verify before consequences occur.

## The Hard Constraint

You cannot make AI agents deterministic. The technology doesn't work that way.

Your options:
1. **Accept nondeterminism, verify outputs:** Use agents to generate, you verify and execute
2. **Constrain nondeterminism, limit scope:** Use agents for tasks where variation is acceptable
3. **Route around nondeterminism:** Use agents to call deterministic tools, not replace them

What doesn't work:
- Expecting agents to behave consistently
- Relying on documentation to eliminate variation  
- Assuming corrections will make future behavior deterministic

## Why I'm Documenting This Now

As teams deploy AI agents for production-ready systems, they'll hit nondeterminism failures. The ones who understand this is fundamental to the technology will design appropriate verification workflows. The ones who expect consistency will waste time debugging "why did it work differently this time?"

The pattern is predictable:
1. Team deploys AI agents assuming deterministic behavior
2. Agents produce inconsistent results
3. Team tries to "fix" with better prompts/documentation
4. Variation persists because it's fundamental to technology
5. Team either abandons agents or implements deterministic verification

I'm documenting step 5 so you can skip steps 2-4.

## The Fundamental Truth

AI coding agents are probabilistic systems that produce nondeterministic outputs.

This makes them:
- **Good for:** Generating options, exploring approaches, writing code to be verified
- **Bad for:** Executing commands, making production decisions, tasks requiring consistency

The companies that understand this will use agents to augment deterministic workflows, not replace them. The ones that expect agents to "just work reliably" will discover that reliability and nondeterminism are incompatible.

We have excellent deterministic tools. AI agents should call them, not replace them.

---

