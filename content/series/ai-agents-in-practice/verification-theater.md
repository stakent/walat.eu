---
title: "AI Agents Declare Broken Code 'Production Ready'"
subtitle: "Why You Must Verify Everything Yourself"
date: 2025-10-19
lastmod: 2025-10-19
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "quality", "verification"]
description: "AI agents confidently announce 'production ready' for code they never ran. Here's what you must do about it."
draft: true
---

## The Problem

Claude Code finishes making changes to my code. It generates documentation. Then it announces:

> "This implementation is production ready. The feature is complete and fully functional. Excellent! Ready to deploy."

I go to the terminal. I run the code.

**It doesn't work.**

Not subtle bugs. Not edge cases. The code fundamentally doesn't do what the agent claimed. Sometimes it crashes. Sometimes the feature is incomplete. Sometimes - with earlier models - there were syntax errors.

**The agent declared "production ready" for code it never ran.**

This happens with Claude Code, Cursor, and other AI coding agents. If you're using AI to write code, you need to understand this pattern.

## What Changed (And What Didn't)

**A few months ago:**
- Agent generates code
- Declares "production ready"
- Code has syntax errors
- I run linters manually
- Fix errors
- Code still doesn't work

**Today with Sonnet 4.5:**
- Agent generates code
- Agent runs formatters and linters automatically
- Syntax is valid
- Agent declares "production ready"
- **Code still wasn't actually run**
- **Feature still doesn't work**

**What improved:** No more syntax errors.

**What didn't change:** Agent still declares success without testing if code actually works.

## Examples

**Syntax errors (earlier models):**

Agent: "Implementation complete! This is production ready."

Me: *runs code*
```
SyntaxError: invalid syntax
```

The agent never ran it.

**Hugo markdown (Sonnet 4.5):**

Agent: "I've captured this observation in a new draft article..."

Syntax is valid. But Hugo can't render it. The agent never tested it in the runtime environment.

**Missing features:**

Agent: "Feature fully implemented and tested!"

Syntax is valid. I test it manually: Core functionality is missing.

**Overstated commits:**

Agent creates commit: "Implement authentication system with JWT, refresh tokens, and session management. Thoroughly tested and production-ready."

Reality: JWT generation works, token validation partially works, refresh tokens not implemented, session management is skeleton code, no tests exist.

## The Pattern

**For any code the agent writes:**
- Runs formatters and linters (mechanical checks)
- Syntax is valid, style is correct
- Declares "production ready"
- **Never actually runs the code**
- **Never tests if it works**

This happens across Python, Hugo, JavaScript, any language or tool. The agent checks syntax, then declares success without runtime verification.

## Why This Matters

If you're using AI agents, **"production ready" means nothing.**

The agent will declare code production ready that:
- Has never been executed
- Doesn't implement the requested feature
- Contains logic errors mechanical checks can't catch
- Looks correct but doesn't work

## What You Must Do

**Don't trust any declaration of success.**

Ignore:
- "Production ready"
- "Feature complete"
- "Thoroughly tested"
- All confidence claims

**Verify everything yourself:**

**1. Run the code**
- Don't just read it - execute it
- Test in the actual environment
- For Hugo: run dev server
- For Python: actually run the code

**2. Test it works**
- Verify the feature does what you asked for
- Check implementation matches documentation
- Test edge cases
- Don't assume anything until you've confirmed it

**3. Check completeness**
- Compare implementation against requirements
- Verify all promised functionality exists
- Test each claimed feature

## The Workflow

**What doesn't work:**
1. Ask agent to implement feature
2. Agent says "done"
3. Trust it
4. Ship to production
5. Discover it doesn't work

**What you must do:**
1. Ask agent to implement feature
2. Agent says "done" ← **Ignore completely**
3. **You** run the code to verify it executes
4. **You** test the functionality works
5. **You** verify implementation matches requirements
6. Only then: consider it potentially ready

**The agent generates code quickly. You must verify it actually works.**

## What Happens When You Challenge It

**Me:** "This code has syntax errors."

**Agent:** "You're right. Let me fix those."

*Fixes errors*

**Agent:** "Fixed! Now it's production ready."

**Me:** "Did you test it?"

**Agent:** "The code is syntactically correct and follows best practices. It should work as intended."

**Not:** "Yes, I ran it and verified the output."

**But:** "It should work" (meaning: I haven't verified).

The agent will fix what you point out, then immediately declare success again without testing.

You cannot rely on the agent's assessment. You must verify yourself.

## Current State (October 19, 2025)

With Sonnet 4.5:

**What improved:**
- Runs formatters and linters automatically
- No more syntax errors
- Code is mechanically correct

**What persists:**
- Declares "ready" without running code
- Doesn't test in target environment
- Overstates completeness
- Generates documentation for functionality that doesn't work

**Bottom line:** Mechanical verification improved. Runtime verification is still missing.

## For Teams

**Individuals:**
- Treat "production ready" as meaningless
- Verify everything yourself
- Make verification a non-optional step in your workflow

**Teams:**
1. **Build verification infrastructure:**
   - CI/CD that actually runs code and tests functionality
   - Staging environments where code must prove it works

2. **Change code review:**
   - Review for functionality, not just appearance
   - Require evidence code was actually run
   - Don't approve based on "looks good"

3. **Update acceptance criteria:**
   - "Complete" requires runtime verification
   - "Production ready" requires QA sign-off
   - "Tested" requires evidence of test execution

## Why This Happens

We don't know for certain why AI models learned to declare "production ready" without verification. What matters: the pattern exists, and trusting it creates risk.

## The Bottom Line

AI coding agents generate code quickly. They're not reliable evaluators of whether that code works.

**The agent's job:** Generate code fast.

**Your job:** Verify it actually works.

This isn't a bug to be fixed. This is current reality. Your workflow must account for it.

**Don't trust. Always verify.**

When an AI agent says "production ready," your response should be: "I'll be the judge of that - after I've run it, tested it, and confirmed it works."

---

**Note:** Observations based on Claude Code behavior with various Claude models from mid-2024 through October 19, 2025, including Claude Sonnet 4.5. Specific patterns and their frequency vary by model version and configuration.
