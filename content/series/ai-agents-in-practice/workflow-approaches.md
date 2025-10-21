---
title: "Five Workflow Approaches for AI Coding Agents"
subtitle: "There Is No Single Best Practice - Choose Based on Your Context"
date: 2025-10-19
lastmod: 2025-10-19
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "workflows", "best-practices", "TDD"]
description: "From test-driven development to spec-first coding: which workflow actually works for your AI-assisted development."
draft: true
---

The default workflow for AI coding agents - brainstorm, plan iteratively, build - is not the only approach. Recent research from 2025 reveals multiple distinct workflow patterns, each optimized for different contexts and goals.

There is no universal "best practice." The effective approach depends on your specific situation: project type, team experience, quality requirements, and timeline constraints.

Here are five validated workflow approaches, when to use each, and what research shows about their effectiveness.

## 1. Test-Driven Development (TDD) with AI Agents

**The workflow:**
1. Write test cases defining expected behavior
2. Have AI agent implement code to pass tests
3. Verify all tests pass
4. Refactor with AI assistance

**Why this works for AI:**

Research shows AI agents thrive on clear, measurable goals:[^1]

> "AI thrives on clear, measurable goals, and a binary test is one of the clearest goals you can give it, making TDD a perfect workflow for AI agents."

AI can generate boilerplate, edge cases, and entire test files in seconds, turning TDD's biggest weakness - the time investment in writing tests first - into a massive accelerator.[^1]

**When to use:**
- Changes with easily verifiable behavior (APIs, data transformations, business logic)
- When correctness is paramount (financial calculations, compliance logic)
- When you can define expected inputs and outputs clearly
- Refactoring existing functionality

**Limitations:**
- UI/UX work where "correct" is subjective
- Exploratory work where requirements are unclear
- Performance optimization where tests don't capture the goal

**Anthropic's recommendation:**

For easily verifiable changes, Anthropic recommends asking Claude Code to write tests based on expected input/output pairs first, then implement.[^2]

This provides the fast feedback loops and clear requirements that make AI agents effective, while protecting from hallucinations and errors that can derail AI-generated code.[^1]

**Modified for AI agents:**

Traditional TDD expects deterministic outputs. With AI agents, outcomes vary, so tests need flexibility:[^1]

Instead of exact answers, evaluate behaviors, reasoning, and decision-making using nuanced criteria like scores, ratings, and user satisfaction - not just pass/fail tests.

## 2. Spec-First Coding (Amazon's "Agentic" Approach)

**The workflow:**
1. Write detailed requirements document
2. Create technical design specification
3. Develop implementation plan
4. Only then: have AI generate code
5. Review against spec

**The surprising trend:**

Industry is moving toward MORE upfront planning with AI, not less. One engineering leader calls this "Agile planning, waterfall execution."[^3]

The idea: retain short development cycles, but pack each cycle with far more upfront planning so AI can execute the plan without constant course-correction.

**Amazon's Kiro experiment:**

Amazon's experimental AI coding environment forces developers to choose between "Vibe" (exploratory chat-driven coding) and "Spec" (plan-first coding). In Spec mode, the tool won't generate any code until you've written requirements, design, and implementation plan.[^4]

**Real-world results:**

In trial projects, following strict spec→design→code sequence produced "good quality" applications that worked as intended on the first try. Experienced AI developers report writing longer prompts and feeding models very detailed plans results in code with far fewer errors than "just winging it."[^3]

As one AI engineer put it: "The agents we have right now need what waterfall provides even more than people do."[^3]

**When to use:**
- Production systems requiring high reliability
- Complex features with multiple integration points
- Team environments where multiple developers coordinate
- When mistakes are expensive to fix

**Why it works:**

Detailed specs eliminate ambiguity that causes AI agents to make wrong assumptions. Remember: models struggle to detect underspecified prompts and default to guessing missing requirements.[^5]

Spec-first forces you to think through edge cases, error handling, and integration points before generating code that might be plausible but wrong.

**Trade-offs:**
- Higher upfront time investment
- Less suitable for exploratory work
- Requires knowing what you want before starting
- Planning overhead may exceed value on simple tasks

## 3. Rapid Prototyping / Experiment-Driven Development

**The workflow:**
1. Generate approach A with AI, review quickly
2. Generate approach B with AI, compare
3. Generate approach C if needed
4. Select best approach, refine with AI
5. Extract lessons, write formal implementation

**The insight:**

When code generation takes 30 minutes instead of 3 days, experimentation replaces planning.[^6]

Instead of extensive upfront planning meetings to avoid costly implementation errors, you can try multiple approaches quickly and select the best based on actual code, not theoretical discussion.

**When to use:**
- Exploring unfamiliar technologies or approaches
- Early-stage projects where requirements are uncertain
- Architectural decisions where trade-offs are unclear
- When learning curve is steep and examples help

**Framework support:**

2025 saw rapid development in frameworks enabling quick prototyping:[^7]
- CrewAI ideal for rapid prototyping
- n8n or Flowise for visual, rapid iteration
- LangChain/LangGraph for complex experiments

**Best practices:**

Start with minimal viable agent (MVA) to test assumptions. Use mock environments and rapid prototyping tools to test early-stage behaviors. Start small, experiment with focused use cases, let experience guide evolution.[^7]

**Limitations:**
- Can produce "prototype quality" code that needs hardening for production
- Easy to waste time exploring approaches that won't scale
- Requires discipline to extract learnings and rebuild properly
- Risk of shipping prototype-quality code to production

**The key:** This is for exploration and learning, not final implementation. Use insights from rapid prototypes to inform proper spec-first or TDD approach for production code.

## 4. Multi-Tool Layering (The Practitioner Approach)

**The workflow:**
1. Use ChatGPT/Claude for initial research and architecture discussion
2. Use Cursor/Claude Code for implementation
3. Use GitHub Copilot for in-editor completions during refinement
4. Return to chat-based tools for debugging complex issues

**The research:**

Most effective practitioners use 2-3 AI tools simultaneously, not relying on single platform.[^8]

- 49% of organizations pay for multiple AI coding tools
- 26% use both GitHub Copilot and Claude Code simultaneously
- Different tools excel at different phases

**Why this works:**

Each tool optimized for different interaction patterns:

**Chat-based (ChatGPT, Claude):**
- Architectural discussions
- Complex problem-solving
- Research and learning
- Debugging conceptual issues

**IDE-integrated (GitHub Copilot):**
- In-editor completions
- Inline explanations
- Quick refactoring suggestions
- Flow-state coding assistance

**Terminal-first agents (Claude Code):**
- Full codebase manipulation
- Multi-file changes
- Deep context understanding
- Natural language task execution

**When to use:**

This isn't a single workflow - it's recognizing that different tasks require different tools. Use the right tool for each phase rather than forcing one tool to do everything.

**Example project flow:**
1. Claude chat: discuss architecture options, identify risks
2. Claude Code: implement core functionality across multiple files
3. GitHub Copilot: refine implementation details in-editor
4. Claude chat: debug integration issues with full context
5. Cursor: apply learnings to final polish

**Trade-offs:**
- Higher tool cost (multiple subscriptions)
- Context switching between tools
- Learning curve for each tool's strengths
- Need to manage different conversation histories

**Benefits:**
- Each tool used for its optimal purpose
- Flexibility to adapt to task requirements
- Reduced frustration from tool limitations
- Better overall results than single-tool approach

## 5. Agentic Workflow Patterns (Orchestration Approach)

**The workflow patterns:**

Research identifies nine transformative agentic workflow patterns for 2025:[^9]

**Plan-Do-Check-Act Loop:**
- Agent plans multi-step workflow
- Executes each stage sequentially
- Reviews outcomes after each step
- Adjusts approach based on results
- Iterates until goal achieved

**Multi-Agent Orchestration:**
- Central "orchestrator" agent breaks down task
- Assigns work to specialized "worker" agents
- Each worker optimized for specific role (frontend, backend, testing, etc.)
- Orchestrator synthesizes results
- Powers RAG systems, complex coding agents

**Parallel Execution:**
- Split large tasks into independent sub-tasks
- Execute concurrently by multiple agents
- Drastically reduces time to completion
- Useful for code review, A/B testing, building guardrails

**When to use:**

This is the enterprise/team approach for complex, multi-faceted projects:
- Large codebases requiring coordinated changes
- Projects with multiple distinct components
- When specialized expertise needed in different areas
- Complex business process automation

**Tools supporting this:**

By 2025, frameworks like LangGraph, AutoGen, and Microsoft Semantic Kernel enable sophisticated multi-agent orchestration at enterprise scale.[^10]

**Trade-offs:**
- Significant setup complexity
- Requires infrastructure for agent coordination
- Higher computational costs (multiple agents running)
- Debugging multi-agent interactions is challenging

**Benefits:**
- Handles complexity beyond single-agent capabilities
- Scales to large projects
- Specialization improves quality in each domain
- Better separation of concerns

## How to Choose Your Workflow

The research reveals no universal best practice. Choose based on context:

**Choose TDD when:**
- ✅ You can define clear input/output expectations
- ✅ Correctness is more important than speed
- ✅ Refactoring existing, well-understood code
- ✅ Working on APIs, business logic, data transformations

**Choose Spec-First when:**
- ✅ Building production systems with high reliability requirements
- ✅ Working with teams requiring coordination
- ✅ Mistakes are expensive to fix
- ✅ You know what you want before starting

**Choose Rapid Prototyping when:**
- ✅ Exploring unfamiliar approaches or technologies
- ✅ Requirements are uncertain
- ✅ Architectural decisions need validation
- ✅ Learning is the primary goal

**Choose Multi-Tool Layering when:**
- ✅ Different project phases need different interaction patterns
- ✅ You can afford multiple tool subscriptions
- ✅ Context switching isn't disruptive for your workflow
- ✅ Single tools frustrate you with limitations

**Choose Agentic Orchestration when:**
- ✅ Projects too complex for single-agent approach
- ✅ You have infrastructure for multi-agent coordination
- ✅ Specialized expertise needed across domains
- ✅ Enterprise scale with appropriate tooling budget

## The Common Thread: Verification

Regardless of workflow choice, effective AI-assisted development requires systematic verification.

**Why verification matters more than generation:**

Research shows teams using AI tools create 98% more pull requests but experience 91% longer review times.[^11] The constraint shifted from generation to verification.

All five workflows share this pattern:
1. Use AI to generate (fast)
2. Verify output systematically (required)
3. Iterate until correct (potentially slow)

**The verification approaches vary:**
- TDD: tests define correctness
- Spec-First: implementation matches specification
- Rapid Prototyping: experiments answer questions
- Multi-Tool: each tool validates others
- Agentic: orchestrator checks worker outputs

**But verification is never optional.**

The workflows that fail are those that skip systematic verification and trust AI output without checks.

## The Learning Curve Reality

METR's July 2025 study found developers using AI tools for the first time took 19% longer to complete tasks, despite estimating 20% speedup.[^12]

**What this means:**

Every workflow has a learning curve. Expect initial slowdown. The 38% speedup achieved by the study's top performer shows skill matters more than tool choice.

Don't judge workflow effectiveness based on first-week results. Measure after developers gain proficiency with both the workflow and the tools.

## Combining Workflows

The most sophisticated teams don't pick one workflow - they use different approaches for different work:

**Example project:**
- Spec-First for core platform features
- TDD for business logic and APIs
- Rapid Prototyping for new technology evaluation
- Multi-Tool throughout all phases
- Agentic Orchestration for large refactoring efforts

The workflow serves the work, not vice versa.

## The Anti-Pattern: Default Chat-Driven Development

**What doesn't work:**

Open-ended prompts like "build me a user authentication system" followed by iterative chat refinement without systematic structure.

**Why it fails:**

Models trained via RLHF prefer complete but presumptuous answers over clarifying questions.[^13] They'll generate code based on assumptions rather than asking about requirements, constraints, and success criteria.

This produces plausible-looking code that doesn't meet your actual needs - the plausible bullshit problem.

**All five workflows above structure the interaction to prevent this:**
- TDD: tests force specific requirements
- Spec-First: documentation eliminates assumptions
- Rapid Prototyping: multiple attempts reveal gaps
- Multi-Tool: different tools surface different issues
- Agentic: orchestrator validates worker assumptions

## What 2025 Research Shows

**Key findings across all workflows:**[^2][^8][^14]

1. **Clear communication matters:** AI tools are only as good as instructions provided
2. **Context is critical:** The more detail provided, the better results
3. **Review is mandatory:** Enhanced code review practices essential for AI-generated code
4. **Documentation standards:** Consistent templates for tools improve agent performance
5. **Governance frameworks:** Matter MORE for AI than traditional development

**Productivity variance:**
- Organizations just deploying AI tools: 10-15% productivity boost
- Organizations restructuring workflows: 26% increase in completed tasks
- The difference isn't better AI - it's workflow optimization[^6]

## The Path Forward

As AI coding agents become standard tools, workflow discipline will separate successful adoption from frustrated abandonment.

**The teams that succeed:**
- Choose workflows based on context, not dogma
- Invest in learning curves for both tools and workflows
- Maintain systematic verification regardless of workflow
- Adapt workflows as tools and team skills evolve
- Measure effectiveness over weeks/months, not days

**The teams that struggle:**
- Expect immediate productivity from unfamiliar workflows
- Use single workflow for all work types
- Skip verification because generation is fast
- Judge tools based on first-week experience
- Copy others' workflows without understanding their context

## The Bottom Line

There is no single best practice for AI coding agents.

Test-Driven Development provides clear goals and fast feedback. Spec-First eliminates ambiguity for complex production systems. Rapid Prototyping enables exploration and learning. Multi-Tool Layering optimizes each phase. Agentic Orchestration handles enterprise complexity.

**The question is not "which workflow is best?"**

The question is: "which workflow matches my current context - project type, team skills, quality requirements, and timeline constraints?"

Choose deliberately. Learn systematically. Verify ruthlessly.

The productivity gains are real - but they come from workflow discipline, not just tool deployment.

---

## References

[^1]: Builder.io (2025). "Test-Driven Development with AI." Analysis of TDD workflow patterns with AI agents, noting that binary tests provide clearest goals for AI and AI-generated test coverage turns TDD's time investment into acceleration. [https://www.builder.io/blog/test-driven-development-ai](https://www.builder.io/blog/test-driven-development-ai)

[^2]: Anthropic (2025). "Claude Code: Best practices for agentic coding." Official engineering guidelines emphasizing test-driven development for verifiable changes and clear communication of intent. [https://www.anthropic.com/engineering/claude-code-best-practices](https://www.anthropic.com/engineering/claude-code-best-practices)

[^3]: Goortani, F. (August 2025). "AI, Agile, and the Return of Detailed Planning: A New Balance for Leaders." Medium. Describes trend toward "Agile planning, waterfall execution" where detailed upfront planning enables AI execution without constant course-correction. [https://medium.com/@FrankGoortani/ai-agile-and-the-return-of-detailed-planning-a-new-balance-for-leaders-469d09831a70](https://medium.com/@FrankGoortani/ai-agile-and-the-return-of-detailed-planning-a-new-balance-for-leaders-469d09831a70)

[^4]: McLeod, S. (June 2025). "Vibe Coding vs Agentic Coding." Analysis of Amazon's Kiro experimental environment forcing choice between exploratory "Vibe" coding and spec-first "Agentic" coding. [https://smcleod.net/2025/06/vibe-coding-vs-agentic-coding/](https://smcleod.net/2025/06/vibe-coding-vs-agentic-coding/)

[^5]: Arora, S., & Hashimoto, T. (2025). "What Prompts Don't Say: Understanding and Managing Underspecification in LLM Prompts." arXiv:2505.13360. Research finding LLMs struggle to detect ambiguity, with underspecified prompts 2x more likely to regress. [https://arxiv.org/abs/2505.13360](https://arxiv.org/abs/2505.13360)

[^6]: Faros AI (2025). "The AI Productivity Paradox Research Report." Telemetry analysis showing teams complete 21% more tasks but review time increases 91%, demonstrating bottleneck shift from generation to verification. [https://www.faros.ai/blog/ai-software-engineering](https://www.faros.ai/blog/ai-software-engineering)

[^7]: MarkTechPost (July 2025). "The 20 Hottest Agentic AI Tools And Agents Of 2025 (So Far)." Overview of rapid prototyping frameworks including CrewAI, LangChain/LangGraph, and emphasis on minimal viable agent (MVA) approach. [https://www.marktechpost.com/2025/07/17/the-20-hottest-agentic-ai-tools-and-agents-of-2025-so-far/](https://www.marktechpost.com/2025/07/17/the-20-hottest-agentic-ai-tools-and-agents-of-2025-so-far/)

[^8]: VentureBeat (2025). "GitHub leads the enterprise, Claude leads the pack—Cursor's speed can't close." Market analysis showing 49% of organizations pay for multiple AI coding tools, with 26% using both GitHub Copilot and Claude Code simultaneously. [https://venturebeat.com/ai/github-leads-the-enterprise-claude-leads-the-pack-cursors-speed-cant-close](https://venturebeat.com/ai/github-leads-the-enterprise-claude-leads-the-pack-cursors-speed-cant-close)

[^9]: MarkTechPost (August 2025). "9 Agentic AI Workflow Patterns Transforming AI Agents in 2025." Analysis of plan-do-check-act loops, multi-agent orchestration, and parallel execution patterns. [https://www.marktechpost.com/2025/08/09/9-agentic-ai-workflow-patterns-transforming-ai-agents-in-2025/](https://www.marktechpost.com/2025/08/09/9-agentic-ai-workflow-patterns-transforming-ai-agents-in-2025/)

[^10]: Machine Learning Mastery (2025). "7 AI Agent Frameworks for Machine Learning Workflows in 2025." Overview of LangGraph, AutoGen v0.4, Microsoft Semantic Kernel for enterprise-scale agentic workflows. [https://machinelearningmastery.com/7-ai-agent-frameworks-for-machine-learning-workflows-in-2025/](https://machinelearningmastery.com/7-ai-agent-frameworks-for-machine-learning-workflows-in-2025/)

[^11]: Bain & Company (2025). "From Pilots to Payoff: Generative AI in Software Development." Technology Report 2025 finding organizations restructuring workflows achieve 26% task completion gains vs 10-15% for basic tool adoption. [https://www.bain.com/insights/from-pilots-to-payoff-generative-ai-in-software-development-technology-report-2025/](https://www.bain.com/insights/from-pilots-to-payoff-generative-ai-in-software-development-technology-report-2025/)

[^12]: METR (July 2025). "Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity." Randomized controlled trial finding 19% longer completion time but 20% perceived speedup, with top performer achieving 38% actual speedup. [https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/)

[^13]: Ravichander, A., Chen, Z., Deng, J., Hashimoto, T., & Kamar, E. (2024). "Modeling Future Conversation Turns to Teach LLMs to Ask Clarifying Questions." arXiv:2410.13788. Research identifying single-turn preference labeling creates bias where annotators prefer presumptuous answers over clarifying questions. [https://arxiv.org/abs/2410.13788](https://arxiv.org/abs/2410.13788)

[^14]: JetBrains (May 2025). "Coding Guidelines for Your AI Agents." Best practices emphasizing clear communication, specific instructions, and context provision for effective AI agent performance. [https://blog.jetbrains.com/idea/2025/05/coding-guidelines-for-your-ai-agents/](https://blog.jetbrains.com/idea/2025/05/coding-guidelines-for-your-ai-agents/)
