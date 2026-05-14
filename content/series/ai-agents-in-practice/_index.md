---
title: "AI Coding Agents in Practice"
date: 2025-10-19
lastmod: 2026-05-14
author: "Dariusz Walat"
slug: "ai-agents-series-introduction"
description: "Operational patterns from building production-ready systems with AI coding agents — snapshots from real engineering as the technology evolves."
---

Articles documenting what actually happens when you build production-ready software using AI coding agents as the primary development method — not as occasional assistance, not for boilerplate, but for all the code.

Each piece is a snapshot from a specific point in time. AI technology moves weekly: what's true about Claude Code's behavior today may not be true next month. Patterns named here may become obsolete as models improve or new tools emerge. The date on each piece matters.

The public narrative about AI coding agents is incomplete. Productivity gains are real. So are the operational realities — agents generating plausible documentation for code they didn't write, multi-agent systems compounding failures, review burden overwhelming generation speed gains, nondeterminism making reliable workflows nearly impossible. These pieces document the second part.

## Published

**[AI Coding Agents Are Plausible Bullshit Generators](/series/ai-agents-in-practice/plausible-bullshit/)**
*October 18, 2025*

When agents generate convincing documentation for functionality they never implemented. Why you must verify everything yourself, and what this means for production systems.

**[Why This Series Can Never Be Finished](/series/ai-agents-in-practice/why-this-series-cannot-be-finished/)**
*October 19, 2025*

When your research subject evolves faster than you can document it. Why documenting rapidly-evolving AI technology requires a fundamentally different approach than traditional software documentation.

## On reading these

{{< disclaimer >}}

These are observations from one engineer building personal projects with specific tools at specific points in time. Your context, your tools, your problems may differ. If you're building production systems with AI coding agents right now, you'll recognize patterns here — they're not specific to my setup; they're characteristics of current AI technology. Use these pieces as starting points for your own research, not as definitive answers. The technology is too new, too fast-changing, and too context-dependent for definitive answers to exist yet.
