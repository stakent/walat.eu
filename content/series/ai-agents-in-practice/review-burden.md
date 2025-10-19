---
title: "AI Agents Generate Code Faster Than You Can Review It"
subtitle: "This Is The Future"
date: 2025-10-18
lastmod: 2025-10-18
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "productivity", "code-review"]
description: "Why hand-coding is obsolete, what review burden actually means, and how productivity transforms when generation isn't the bottleneck."
draft: true
---

Here's the uncomfortable but exciting truth about what happens when AI agents succeed at generating code:

**You get more code than you can review - so you focus on architecture and verification instead of "typing," which is exactly where we need to be.**

The claims are true: AI coding agents - especially swarms working in parallel - can generate code 10x, 100x, even 1000x faster than traditional development.

Hand-coding is obsolete for most development work. Not because AI is perfect - it's not. But because the time saved on "typing" enables you to work at a higher level: architecture, verification, and creating actual business value.

The important question: **What does this enable you to do instead of "typing" code?**

## The Post-Generation Reality

You've run your multi-agent swarm. Thirty minutes to two hours later, you have shiny new changes implementing your feature. The agents report success. Changes exist - but whether they work correctly is another question.

**Now you do the work that actually matters.**

**Step 1: Automated verification (30 seconds)**

Run code formatter. Run static analysis. Run linting tools.

These catch the mechanical errors:
- Formatting violations (auto-fixed)
- Duplicate enum definitions (flagged instantly)
- Bare exception handlers (detected by linter)
- Complexity metrics violations (highlighted for review)

**Time investment:** Seconds to minutes, fully automated.

**Value:** Mechanical correctness without manual effort.

This is exactly the kind of work humans shouldn't be doing. Let tools verify tools.

**Step 2: Automated testing (5-30 minutes)**

Run the full test suite. Every test that existed before must still pass.

**Critical constraint:** The agent generated new code. It did NOT modify your tests (and you must prevent it from doing so).

**When tests fail:**
- Feed the failures back to the coding agent
- Explicitly instruct: "Fix the implementation to pass these tests. DO NOT modify the tests themselves."
- Review the fixes to ensure the agent didn't cheat by weakening test assertions
- **Return to Step 1:** Run automated verification again on the fixed code

**Why the loop back matters:**

When the agent fixes code to pass tests, it may introduce new mechanical issues:
- Formatting violations in the new code
- Linting errors from hasty fixes
- Complexity issues from workarounds
- New duplicate code patterns

**The verification cycle:**
1. Agent generates code → Automated verification → Tests
2. Tests fail → Agent fixes code → **Back to automated verification** → Tests
3. Tests pass → Continue to code review

**Why this matters:**

Tests are your contract. They define correct behavior. If the agent's code breaks tests, the agent's code is wrong - not the tests.

Allowing agents to modify tests is how you ship bugs with passing CI.

**Time investment:** 5-30 minutes depending on test suite size (may iterate 2-3 times).

**Value:** Functional correctness verified automatically. Bugs caught before human review. All mechanical checks still pass after fixes.

**Step 3: Code review (the valuable work)**

This is where you discover what the code actually does, and this is where your expertise matters.

**Example: A verbose one-line setter**

The agent created:
```python
def set_count(self, value: int) -> None:
    """
    Set the count field of the object.

    This method updates the internal count state by assigning
    the provided value to the count attribute of this instance.
    The count represents the number of items processed and is
    used throughout the system for tracking purposes.

    Args:
        value: The new count value to set

    Returns:
        None

    Example:
        >>> obj = MyClass()
        >>> obj.set_count(42)
    """
    self.count = value  # Save count in the count field
```

**Your review (5 seconds):** "This method is unnecessary. Should use direct attribute access: `obj.count = value`

**Your instruction to agent:** "Remove `set_count()` and all similar verbose setter/getter methods. Replace with direct attribute access. Search all generated code for this pattern and fix everywhere. Follow Python's principle: simple attribute access unless you need validation or side effects."

**Agent's refactoring pass:** Finds and removes 12 similar verbose setters across the codebase, replacing with direct attribute access.

**Your verification (30 seconds):** Scan the changes. All appropriate. Run automated verification and tests to confirm.

**Result:** 240+ lines of unnecessary code generated in seconds, identified by human in seconds, removed by agent in minutes through pattern-based instruction.

**This is the future.** You're not "typing" deletions. You're making architectural decisions about what code should exist at all, then instructing the agent to apply those patterns everywhere.

## The Shift: From "Typing" To Thinking

<!-- FIXME: Research shows developers spend only ~16% of work time on development, and within that, reading:writing ratio is 10:1. The 80% typing claim is inaccurate. Need to reframe this section based on: (1) what constraints slow typing created in the workflow, (2) how AI removes those constraints, (3) what becomes possible when generation isn't the bottleneck. The real story isn't "speed up typing" but "eliminate organizational overhead that exists because typing was slow." -->

**Traditional development:**
- 80% "typing" code
- 20% thinking about architecture, design, business logic

**AI-augmented development:**
- 5% instructing agents what to generate
- 15% reviewing and refining generated code
- 80% thinking about architecture, design, business value

The time spent "typing" is replaced by time spent on higher-value activities.

## What Review Actually Teaches You

Reviewing thousands of lines of AI-generated code isn't tedious - it's educational:

**You learn what agents get wrong consistently:**
- They over-comment obvious code
- They create unnecessary abstractions
- They duplicate logic instead of extracting it
- They optimize for "looking professional" over simplicity

**This makes you better at instructing agents:**

Next time, you prompt: "Generate this feature. No docstrings for obvious setters. Extract common logic to shared functions. Optimize for simplicity, not line count."

The agent improves because you learned from reviewing its patterns.

## The Productivity Transformation

**The old question:** "How fast can I type this code?"

**The new question:** "What code should exist, and how do I verify it works?"

**Time breakdown - Traditional hand-coding:**
- Typing implementation: 6 hours
- Code review (self): 1 hour
- Testing: 1 hour
- **Total: 8 hours**

**Time breakdown - AI-augmented:**
- Instructing agent: 30 minutes
- Agent generates: 30 minutes
- Automated verification: 5 minutes
- Running tests + fixing failures: 30 minutes
- Code review: 2 hours (more code to review)
- Refactoring generated code: 1 hour
- **Total: 4.5 hours**

**Time saved: 3.5 hours**

**But here's what matters:** Those 3.5 hours aren't "saved" - they're **redirected to higher-value work:**
- Designing better architecture
- Improving test coverage
- Documenting system decisions
- Planning next features
- Reviewing other engineers' work

## The Review Capacity "Problem" Is Actually The Point

**Yes, agents generate more code than you can review as carefully as hand-written code.**

**This is not a bug. This is the feature.**

You're forced to:
- **Think architecturally:** "Should this code exist at all?"
- **Review strategically:** "What are the high-risk areas?"
- **Automate verification:** "What can tools check?"
- **Refactor ruthlessly:** "How do I simplify this?"

Hand-coding let you be sloppy about architecture because "typing" was the bottleneck. AI generation makes architecture the bottleneck, which is exactly where it should be.

## What AI Generation Enables

**You can now:**

**Experiment rapidly:** Generate three different approaches, review each, pick the best. Cost: 30 minutes instead of 3 days.

**Refactor aggressively:** "Reorganize this entire module" takes 20 minutes to generate and 1 hour to review, instead of 2 days to type carefully.

**Explore unfamiliar domains:** Agent generates initial implementation in technology you don't know well, you review and learn the patterns, iterate to production quality.

**Maintain legacy systems:** Agent reads old codebase, generates modern equivalent, you verify behavior matches. Migrations that took months now take weeks.

**Scale your impact:** One engineer with agents can accomplish what took a team, because "typing" is no longer the constraint.

## The Deployment Reality

**Can you deploy AI-generated code directly to production?**

With appropriate verification infrastructure, absolutely:

**Automated verification catches mechanical errors:**
- Formatting tools ensure style compliance
- Static analysis catches obvious bugs
- Type checkers verify interface contracts
- Linters enforce complexity limits

**Tests catch functional errors:**
- Unit tests verify behavior
- Integration tests verify composition
- E2E tests verify user workflows
- Performance tests verify efficiency

**Human review catches architectural errors:**
- "This violates our design principles"
- "This creates tight coupling"
- "This duplicates existing functionality"
- "This is more complex than needed"

**The combination works.** Automated tools handle mechanical verification. Humans handle architectural judgment.

This is exactly how software should be built: tools verifying tools, humans making decisions.

## The Correct Use Pattern

AI agents are transformative for:
- **All boilerplate code:** Generated in seconds, reviewed in minutes
- **Initial implementations:** Three approaches in the time one took before
- **Refactoring work:** Mechanical transformations done instantly
- **Unfamiliar technologies:** Agent generates, you learn by reviewing
- **Scaling individual impact:** Do work that previously required teams

AI agents still require human judgment for:
- **Architecture decisions:** What code should exist?
- **Security verification:** Where are the vulnerabilities?
- **Performance validation:** Is this efficient enough?
- **Maintainability assessment:** Can we modify this later?

But these are exactly the high-value activities engineers should focus on, not "typing" implementations.

## Why Hand-Coding Is Obsolete

**Hand-coding made sense when:**
- Typing was faster than explaining to someone else
- Code review was more expensive than writing correctly first
- Tools couldn't verify mechanical correctness

**None of these are true anymore:**

- Explaining to agent (30 min) faster than "typing" (6 hours)
- Code review necessary regardless of who writes code
- Tools verify mechanical correctness better than humans

**Hand-coding generates no value except in limited cases:**
- Security-critical code where subtle bugs are catastrophic
- Performance-critical code where every instruction matters
- Safety-critical systems where human lives depend on correctness

For everything else - which is 95%+ of software development - AI generation with human review is simply better:
- Faster to produce
- Forces architectural thinking
- Enables rapid experimentation
- Scales individual impact

## The Productivity Multiplier

**The claim:** "AI makes you 10-100x more productive!"

**The nuanced reality:**
- Code generation: 100x faster (true)
- Code review: 2-5x more time required (also true)
- Architecture work: Unlimited time now available (the real win)

**Net effect:** You produce better systems faster because you're thinking about architecture instead of "typing."

The productivity gain isn't in lines of code. It's in system quality and business value delivered.

## Why I'm Documenting This Now

The future of software development is clear:
- AI agents generate implementations
- Automated tools verify mechanical correctness
- Humans make architectural decisions
- Code review focuses on what matters

Teams that embrace this will ship better software faster. Teams that resist because "AI isn't perfect" will be outcompeted by teams that use imperfect AI tools effectively.

The companies that understand review is the valuable work - not the "typing" - will build better systems with smaller teams.

## The Optimistic Truth

Current AI technology generates code faster than you can review it carefully.

**This is good.**

It forces you to:
- Stop "typing" and start architecting
- Stop reviewing minutiae and start reviewing design
- Stop being a code-generating machine and start being an engineer

Hand-coding was always the least valuable part of software development. We did it because we had to.

We don't have to anymore.

**AI agents free you to do the work that actually matters:** designing systems, verifying correctness, delivering business value.

The review burden is real. But it's a burden that replaces the worse burden of "typing" code by hand.

**I'll take architecture and review over "typing" any day.**

That's not a productivity loss. That's the future of software engineering.

---

