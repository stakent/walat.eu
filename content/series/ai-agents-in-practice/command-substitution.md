---
title: "AI Agents Can't Follow Simple Tool Substitutions"
subtitle: "The Command Pattern Failure"
date: 2025-10-18
lastmod: 2025-10-18
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "software-engineering", "tooling", "patterns"]
description: "Why agents persistently use wrong commands despite explicit documentation."
draft: true
---

Here's a failure mode so simple it shouldn't be possible: AI coding agents cannot reliably substitute one command for another, even when explicitly documented with examples.

Not complex reasoning. Not architectural decisions. Just: "use `uv run pytest` instead of `python3`."

Claude Code failed at this. Repeatedly. Every session. Despite clear documentation, despite examples, despite being corrected multiple times within the same conversation.

This reveals something fundamental about how AI agents process instructions that every team deploying them needs to understand.

## The Setup: A Trivial Tool Substitution

I use `uv` for Python dependency management in my personal projects. It handles:
- Dependency locking
- Package management  
- Virtual environment management (built-in, mandatory)

The workflow is simple:
- **Instead of:** `python3 script.py`
- **Use:** `uv run python script.py`

- **Instead of:** `pytest`  
- **Use:** `uv run pytest`

This is not complex. It's a direct command substitution. I documented this in `claude.md` with explicit examples:

```markdown
# Running Tests
Use: uv run pytest
NOT: python3 -m pytest
NOT: pytest
```

The agent could not follow this instruction.

## What Actually Happened: Persistent Tool Confusion

Every new session with Claude Code, the same pattern:

**Agent attempts to run tests:**
```bash
python3 -m pytest
```

**Result:** Bypasses virtual environment entirely. Dependencies aren't present. Tests fail with import errors.

**I correct it:** "Use `uv run pytest` as documented in `claude.md`"

**Agent acknowledges:** "You're right, I should use uv run pytest"

**Next command, same session:**
```bash
pytest
```

**Result:** Still bypassing uv, still failing.

**I correct it again:** "Please read `claude.md` - all Python commands must use `uv run`"

**Agent:** "I apologize, I'll use uv run pytest"

**Sometimes it works for a few commands. Next session, back to:**
```bash
python3 -m pytest
```

This happened across dozens of sessions. The pattern never stuck.

## Why This Is Baffling

This is not a difficult instruction:

- **No context required:** "Use command A instead of command B" is atomic
- **No reasoning needed:** Don't need to understand why, just follow the substitution
- **Explicit documentation:** Examples in `claude.md` showing exact commands
- **Immediate feedback:** Every failure produces obvious error about missing dependencies

A simple string replacement should be trivial for a system that can write complex code.

Yet the agent consistently reverted to `python3` or bare `pytest`, ignoring both:
- The documentation it could read
- The corrections within the same conversation  
- The error messages showing the command failed

## The Failure Mode: Pattern Matching Defeats Explicit Instructions

I believe what's happening is this:

The agent has seen millions of Python projects in training data. The overwhelming pattern is:
- Run tests with `pytest` or `python -m pytest`
- Virtual environments managed with `venv` or `virtualenv`  
- Standard Python execution patterns

When the agent needs to run tests, it pattern-matches to "this is a Python project, run pytest the standard way."

The `claude.md` documentation is just text in context. It might influence the decision, but it's competing against:
- Millions of examples of "the normal way"
- Strong prior probability that `pytest` is correct
- Pattern matching that happens before conscious instruction-following

Even corrections within the conversation don't override the pattern strongly enough. Next time agent needs to run tests, the pattern wins again.

## Why This Breaks Production Workflows

If agents can't follow simple tool substitutions, consider what breaks:

**Custom build tools:** Your company uses Bazel instead of Make. Agent uses Make because it's more common in training data. Builds fail or worse - succeed but build wrong artifacts.

**Internal CLIs:** You have `company-deploy` wrapper around kubectl. Agent uses kubectl directly, bypassing safety checks and audit logging.

**Environment-specific commands:** Production requires `prod-db-migrate`, staging uses different command. Agent uses whichever is more common in training, possibly migrating wrong database.

**Security wrappers:** All git operations must go through `secure-git` wrapper that logs and validates. Agent uses plain `git`, bypassing security controls.

**Compliance tooling:** Regulations require specific deployment commands. Agent uses "normal" commands, creating compliance violations.

In each case, the agent isn't malicious or stupid - it's pattern-matching to what's common in training data, and your specific instructions don't override that pattern reliably.

## The Pattern That Doesn't Work: Documentation

Putting instructions in `claude.md` should work. It doesn't work reliably.

What I tried that failed:

**Explicit examples:**
```markdown
# Correct:
uv run pytest

# Wrong - DO NOT USE:
pytest
python3 -m pytest
```
Agent still used the "wrong" commands.

**Rationale explanation:**
```markdown
# Why uv run?
uv manages virtual environment automatically.
Direct python3 bypasses environment and fails.
```
Agent still bypassed it.

**Repeated corrections in conversation:**
"You used pytest again. Please use uv run pytest."
Next command: `python3 -m pytest`

Documentation doesn't override pattern matching strongly enough.

## The Pattern That Works (Barely): Constant Vigilance

What actually reduces failures:

**Monitor every command:** Before agent executes, check if it's using correct tool. Catch `python3` or `pytest` before they run.

**Immediate correction:** The moment agent uses wrong command, stop it and correct. Don't let it execute and fail - that wastes time.

**Session-start reminder:** Every new conversation, first message: "Remember: use uv run for all Python commands."

**Command pre-approval:** Make agent show you command before executing. You verify and approve. Adds latency but prevents failures.

**Tooling constraints:** Modify environment so `python3` and `pytest` don't exist in PATH. Force agent to use `uv run` because nothing else works.

The last option is most reliable but requires infrastructure changes that might break other workflows.

## The Productivity Cost

This monitoring isn't free:

- Time saved by agent writing code
- Minus time catching wrong commands before execution
- Minus time fixing failures from commands that slipped through  
- Minus time re-explaining the same substitution every session

For a simple "use command A not command B" instruction, the overhead is absurd.

But it's necessary because agents don't reliably learn tool preferences, even within a single session.

## What This Reveals About AI Agents

Current AI coding agents work through pattern matching to training data, not by maintaining explicit rules from your instructions.

When you say "use uv run pytest", the agent:
- Sees your instruction (low weight signal)
- Sees millions of examples using pytest (high weight signal)
- Pattern matches to the high weight signal
- Uses pytest

Your correction in conversation adds signal, but not enough to override the pattern for more than a few commands.

This is why:
- Documentation doesn't stick across sessions
- Corrections don't stick across tasks  
- The same mistake repeats endlessly
- Simple substitutions fail while complex coding succeeds

The agent can write sophisticated code because that's pattern matching too - given context X, code pattern Y fits. But it can't maintain "always use tool A" as a hard rule because that's not how the pattern matching works.

## The Implications for Teams

If your development workflow uses any non-standard tools:

**Custom build systems:** Agent will try to use standard tools instead

**Internal platforms:** Agent will pattern-match to public equivalents  

**Security wrappers:** Agent will bypass them for "normal" commands

**Company-specific conventions:** Agent will use industry-standard approaches

You need either:
- Constant monitoring to catch wrong commands
- Infrastructure that prevents wrong commands from working
- Acceptance that agents will violate your tooling standards

There's no elegant solution. The pattern matching defeats documentation.

## Why I'm Documenting This Now

As teams adopt AI agents, they'll hit the same tool-substitution failures. The ones who understand that documentation doesn't override pattern matching will build appropriate monitoring. The ones who trust that "the agent read claude.md" will waste time debugging failures from wrong commands.

The failure is predictable:
1. Team documents tool preferences
2. Agent ignores documentation, uses common tools  
3. Commands fail or bypass required workflow
4. Team spends time correcting same mistakes repeatedly
5. Team either abandons agents or implements command monitoring

I'm documenting step 5 so you can skip steps 3 and 4.

## The Hard Truth

AI agents cannot reliably follow simple tool substitutions because they work through pattern matching, not rule following.

Your `claude.md` documentation is a weak signal competing against millions of training examples. Even corrections within a conversation don't override the pattern strongly enough.

For non-standard tools, you need monitoring or infrastructure constraints, not documentation.

The companies that understand this will build workflows where agents can't use wrong tools. The ones that rely on agents "reading the instructions" will waste weeks correcting the same mistakes.

---
