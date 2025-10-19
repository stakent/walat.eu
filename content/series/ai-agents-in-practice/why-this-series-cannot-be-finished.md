---
title: "Why This Series Can Never Be Finished"
subtitle: "When Your Research Subject Evolves Faster Than You Can Document It"
date: 2025-10-19
lastmod: 2025-10-19
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "research", "meta"]
description: "I was writing an article about AI agent nondeterminism. Hours later, I realized parts of it were already outdated. This isn't a bug - it's proof of why documenting rapidly-evolving technology requires a fundamentally different approach."
draft: false
---

## What Just Happened

I was working on an article about AI agent nondeterminism. The core thesis: AI agents produce unpredictable outputs because they're probabilistic systems.

One of my examples:

> When I ask Claude Code to run tests, I might get:
> - `uv run pytest` (correct)
> - `pytest` (wrong, bypasses environment)
> - `python3 -m pytest` (wrong, bypasses uv)
>
> Same context. Same documentation. Same conversation history. Different command each time.

Hours after writing that section, I caught the problem:

**This was true. Past tense.**

With Sonnet 4.5, Claude Code now consistently runs `uv run pytest` and proactively executes formatters and linters, exactly as documented in my project's `.claude/CLAUDE.md` file.

The behavior I described as a fundamental limitation **improved significantly** with a model update.

**My article became partially outdated the same day I was writing it.**

## The Deeper Issue

This isn't about one example being wrong. This reveals something fundamental about documenting AI technology:

**The research subject evolves faster than you can document it.**

Traditional software frameworks:
- Released 5 years ago, work mostly the same today
- Documentation stays relevant for years
- Best practices remain stable
- Breaking changes are rare, announced, versioned

AI models:
- Released 3 months ago, superseded twice
- Documented behavior changes with each model update
- Operational patterns shift as capabilities improve
- Changes arrive without warning through API updates

**You cannot write "finished" documentation for a moving target.**

## The Reality of Fast-Moving Technology

I knew from the start that this subject evolves rapidly. That's why every article is timestamped and includes a reminder that observations are tied to specific models and timeframes.

**But I didn't expect the technology to change faster than I could finish writing about it.**

That's what happened here. The behavior I was documenting improved while I was still documenting it.

This isn't about being right or wrong. It's about the actual pace of change in this space.

## The Meta Lesson: This Article Proves Its Own Point

This article exists because:
1. I documented AI agent behavior
2. Technology improved
3. Documentation became outdated
4. I'm now documenting the documentation becoming outdated

**This is recursive.**

But unlike my technical articles, this one cannot become outdated. It's not documenting AI behavior - it's documenting the **permanent condition** that AI behavior evolves faster than documentation.

Three months from now, better models will arrive. My technical documentation will need updating. But this article's thesis - that you cannot write "finished" documentation for a moving target - will remain true.

**It remains true as long as AI technology continues evolving. And that's not going to stop.**

Not because any single company will keep innovating forever, but because the competitive landscape ensures continuous change. When major players pause, new entrants accelerate. The distributed, competitive nature of AI research makes stopping impossible.

## What This Means for the Series

**This series can never be "finished."**

It can be:
- **Paused** - "I've stopped working on this temporarily"
- **Abandoned** - "I'm no longer maintaining this"
- **Ongoing** - "I continue to document as technology evolves"

But marking it **"finished" or "done" is fundamentally impossible** because the underlying technology changes continuously.

**Each article is a timestamp:**
- What was true when I wrote it
- With the models available then
- Based on behavior observed at that time

Not eternal truth. **Documented observation.**

## Why This Approach Matters

Most AI content pretends to be timeless:
- "Best practices for AI coding agents" (which model? which version?)
- "How to use Claude Code effectively" (as of when?)
- "AI agent limitations" (with current models, future ones, or models from last week?)

**This creates false confidence.** Readers assume the information is current. Often it's not.

**My approach:**
- Every article timestamped
- Observations explicitly tied to specific models/timeframes
- Acknowledgment that findings will become outdated
- Commitment to updates as technology changes

**This is grounded in observable reality.**

It's also more useful - because readers know they're getting documented observations from a specific point in time, not universal truths that might be weeks or months out of date.

The timescale itself has shifted. Traditional frameworks stayed valid for years. AI observations can become outdated in weeks. That fundamental change in validity timescale is part of what makes this technology different.

## The Philosophical Point

We're in a period where **technology evolves faster than documentation.**

This requires rethinking how we write about technology:

**Old model:** Document thoroughly, publish once, reference for years.

**New model needed:** Document observations, timestamp clearly, update continuously, acknowledge obsolescence.

**The shift:** From static reference to living research.

This series is an experiment in that new model.

## Why I'm Publishing This

Because catching my own article becoming outdated **while I was still writing it** perfectly illustrates why this approach is necessary.

If I pretended the nondeterminism article was timeless truth, I'd be lying. It documented true behavior that has since improved (in some aspects, with some models, some of the time - see how qualified that needs to be?).

By publishing this meta-commentary, I'm doing three things:

1. **Documenting the reality:** Technical articles become outdated on a weeks-to-months timescale
2. **Demonstrating the pattern:** This will happen repeatedly as technology evolves
3. **Setting expectations:** Readers should expect continuous updates, not finished documentation

## What This Means for You

If you're reading this series:

**Check the dates.** An article from October 2025 describes October 2025 technology.

**Expect updates.** I'll revise as technology changes and my understanding improves.

**Treat as observations, not doctrine.** What I document is what I observed, when I observed it, with the tools available then.

**Verify with current tools.** The behavior I describe might have improved, changed, or disappeared by the time you read this.

This isn't documentation. It's **research notes from a moving frontier.**

## The Commitment

I commit to:
- **Timestamping clearly:** Every article shows when it was written and last updated
- **Updating as technology changes:** When model improvements invalidate documented behavior, I'll revise
- **Acknowledging uncertainty:** What's true today might not be true tomorrow
- **Being transparent:** When I get something wrong or technology improves, I'll document that too

I do NOT commit to:
- **Timeless documentation:** That's impossible with rapidly-evolving technology
- **Universal truth:** Only documented observations from specific contexts
- **Finished research:** The series continues as long as I'm working with AI agents

## Why This Series Exists

To document what actually happens when you build production software with AI coding agents **right now.**

Not theory. Not speculation. Not "best practices" copied from other articles.

**Operational patterns from building real systems with current technology.**

And when "current technology" changes - as it just did, as it will again - the documentation changes too.

That's not a bug. **That's the entire point.**

---

