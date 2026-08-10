---
title: "Token Counting for Fun and Profit"
subtitle: "Context Accumulation and Cache Expiry"
date: 2026-08-10
lastmod: 2026-08-10
author: "Dariusz Walat"
series: ["AI Agents in Practice"]
tags: ["AI", "claude-code", "measurement", "cost"]
description: "Two factors that accumulate from how you work rather than from settings you pick: what you put in the context, and whether the cache is still warm. Measured from local Claude Code transcripts."
draft: false
---

One skill invocation in a Claude Code session wrote 261,974 tokens into the prompt cache. The tokens replayed from cache on each subsequent request went from 93,786 to 355,760, and 93,786 + 261,974 = 355,760 exactly. Every turn after that point cost several times what the turns before it cost, for the rest of the session. That arithmetic is the mechanism confirming itself.

Model choice and reasoning effort both change how fast a session burns through a subscription usage limit, and both are settings you pick deliberately. Two more are not settings at all. They accumulate from how you work, turn by turn, mostly unobserved: what you put into the context, and whether the cache is still warm when you come back. Those two are the subject here.

You cannot optimise what you do not measure. Not poorly, not approximately: not at all. So I measure this, because I want to know what a session costs while it is still running rather than after it stops. On a subscription plan the stop is real and it does not negotiate.

It destroys nothing. I have restarted the same work when the next window opened, and it cost nothing but the wait. What the stop takes away is the ability to do anything with what the agent produced. A half-finished body of work cannot be tested, reviewed, or evaluated, and dwelling on it in that state teaches nothing. Work cut into stages leaves something finished enough to be worth handling while the window is closed.

The project loses its momentum. Time, your limited asset which you intended to spend on the project, has to be redirected.

The measurement behind this is a single session on one machine on 8 August 2026, running `claude-opus-5`. It is a first attempt at instrumenting the problem, not even a preliminary result. Quoting costs to the token would be false precision, and wrong by the next model release regardless. The mechanisms and the orders of magnitude are what survive, so those are what I state.

## What the context content costs

The context window is a capacity limit. The context content is a recurring bill. These get confused, and the confusion is expensive.

There is no session on the server. Every request re-sends the entire conversation from byte zero: system prompt, tool definitions, CLAUDE.md, every previous message, every file read, every tool result. What feels like a continuing conversation is a byte string that is resent in full, and grows, on every single turn.

Resending all of it every turn would be ruinous at full price, and a prompt cache is what stops it being ruinous. The cache holds the work already done on the unchanged front of that string, and replaying it costs roughly a tenth of computing it fresh. **Warm** means the cache is live and you are paying the tenth. **Cold** means it is not, and you are paying full rate or worse.

The window gauge is labelled as remaining capacity, so 40% used reads as plenty of room left. Read as content size it says the opposite. Forty percent of a million-token window is four hundred thousand tokens sitting in the context, and warm, you pay about a tenth of that on every remaining turn. The number that looks like reassurance is the per-turn bill, and it is the only cost signal already on your screen. Put a large file in at turn five of sixty and you pay a tenth of it fifty-five more times.

Two consequences follow directly.

**Per-turn cost is not constant within a session.** Across the measured session the replay per request grew by nearly seven times from first request to last. Most of the jump came from the single skill load in the opening paragraph; the rest was ordinary accumulation. The same task attempted late in a loaded session costs several times what it costs early in a fresh one.

**Total session cost grows faster than session length.** Content only grows, and the number of turns paying for that content grows too, so cost grows as the product of the two. Doubling the length of a session more than doubles what it consumes.

The shape of a file read follows from that:

    cost ~= size          (once, written into the cache)
          + size/10 * N   (replayed on each of the N turns that follow)

The read is not the expense. The tail is. After a dozen or two follow-up turns, re-sending the file has cost more than reading it did in the first place.

This is not an argument against reading things. A design document read early and carried for the rest of a long session costs a fraction of one percent of the session. That is cheap, and starving context to save it is a bad trade. The expensive items are different in kind: a skill that writes a quarter of a million tokens, a whole test log, a corpus dump, and re-reads.

Re-reads deserve their own line. A second read of a file appends a second copy. It does not replace the first. You now carry both, on every remaining turn.

What I changed, in order of effect:

1. **`/clear` when the topic changes.** `/rename` first so the session stays findable. This is the only move that takes conversation length back to zero. `/compact` shortens the history instead, and pays to produce the summary it keeps.
2. **Keep bulk out of the main thread.** Test output to a file, then inspect the file. Corpus queries and multi-file sweeps to a subagent that returns a summary.
3. **Locate, then slice.** Grep or glob returns a few hundred tokens and says where to look. Then read a line range rather than a whole file.
4. **Load skills and other heavy context late, or not at all.** See the opening paragraph.

## What cache invalidation costs

That tenth is what makes long sessions affordable. It is also the thing most easily destroyed by walking away from the keyboard.

The cache is not storing "the file" or "your message." It is storing the work of having read everything up to point N.

    key   = hash of the exact byte sequence of the prompt from position 0 to a marked point
    value = the model's internal attention state after processing those bytes
    match = prefix only, byte-exact

Change one byte at position 500 and everything from 500 onward is recomputed. Because a conversation only appends, each turn's prompt is `[last turn's entire prompt] + [what is new]`. The old part matches the cache; the new part is computed and stored. Render order is fixed — tools, then system, then messages — so anything that changes the front of that string, such as switching model or changing the tool set, invalidates everything after it.

Cache lifetime is about an hour, and that single fact is the whole of the break problem. Work straight through and the prefix stays warm: you keep paying the tenth, and nothing unusual happens. Step away for longer than the hour and the entire prefix has to be computed again from scratch when you return. On identical tokens, unchanged work you already paid for, that is more than an order of magnitude more expensive — concentrated into the one turn where you pick back up.

Three moves are available on returning cold, and they differ by a lot:

- **Continue where you left off.** The most expensive option twice over: one very costly resume turn, and then you go on paying for the whole accumulated context on every turn after it.
- **`/compact` first.** Worse. Compaction reads the entire cold context in order to summarize it, which is precisely the worst case. If you compact at all, do it while the cache is still warm.
- **`/clear`, then read a short handoff note.** The cheapest by a wide margin. The resume turn is a fraction of the cost of continuing, and every turn after it is cheaper too.

Clearing wins twice. It avoids the cold read, and it discards accumulated context that would otherwise be taxed on every remaining turn. The miss is a one-time penalty. The accumulated context is a standing one. On Pro and Max plans, accepting the built-in "resume from a summary" offer is the same move.

The handoff note is the interesting artifact here. Written before the break, it costs a few hundred tokens and turns an expensive resume into a cheap one. Its more useful property is that it is complete at the moment it is written. It is small, it stands without the session that produced it, and it is the only thing in this measurement that is finished while the work still is not.

## What the meter counts

Every request reports four token counts, and they do not cost the same. In ascending order:

- **Cache reads** — prefix already computed, replayed. Roughly a tenth of a fresh input token, and the cheapest thing that happens.
- **Fresh input** — the unit everything else is measured against.
- **Cache writes** — computed now and stored for next time. Somewhat more than fresh input, because you are paying to compute it and to keep it.
- **Output** — everything the model generates, thinking included. Several times a fresh input token, and by far the most expensive per token.

The per-token ranking is stable. What matters more is the volume each class carries, and there the ordering inverts. In the measured session cache reads were about half the weighted total, cache writes most of the rest, output under a tenth, and fresh typed input a rounding error. The cheapest class per token dominated the bill because it is the one that repeats on every turn.

Reading the current usage bars does not require an open session:

    claude -p '/usage'

It prints session percentage, weekly percentage, per-model weekly percentage, reset times, and behaviour flags in a few seconds. The percentages come from the server. The attribution breakdown printed underneath is computed from local session history on that machine only, and excludes other devices and claude.ai.

The tally itself comes from the transcript files at `~/.claude/projects/<project>/<session-uuid>.jsonl`, where assistant entries carry `message.usage` with `input_tokens`, `cache_creation_input_tokens`, `cache_read_input_tokens`, and `output_tokens`. One trap is worth the warning: the transcript logs one row per content block, not per request, so a request with a thinking block, a text block, and a tool-use block appears three times carrying the same usage object. Summing rows inflated my first totals by more than half. Deduplicate by `message.id` and take one row per request.

A last piece of accounting that is easy to miss: a session begins with tens of thousands of tokens already in the context — system prompt, tool definitions, CLAUDE.md, memory index — before you type anything at all. Every fresh session pays that, and every subagent pays its own copy.

## Three things that looked like levers and were not

**Verbosity.** Output is the most expensive class per token and still a small share of the bill, because it is the smallest by volume. Halving how much the model writes saves a few percent of a session. Reasoning effort moves that same small term directly, which bounds what it can save head-on. Its larger effect is indirect: higher effort produces more tool calls, and every tool result lands in the context, where the first mechanism prices it on every turn that follows.

**What I type.** Fresh typed input across an entire working session came to a few hundred tokens. Writing shorter prompts to save budget optimises a rounding error.

**Keeping the cache warm with periodic pings.** This one is arithmetically tempting, and it fails on second inspection. Hourly pings only break even against a single cache miss after most of a day. Worse, each ping is a full turn that also generates output and grows the conversation, so it feeds the accumulation it was meant to protect you from. `/clear` plus a note beats it under every assumption I tried.

I also stopped polling `/usage`. The endpoint is rate limited, and when it refuses it serves the last bars it loaded within the past hour with a "showing last-known usage" note. That is stale data wearing a live face. Once or twice per session at a natural checkpoint is the right cadence, and each call spawns a Claude Code process besides.

## Where this is soft

This is one session, on one machine, on one model, tallied once. Every direction in it is solid and every magnitude is approximate. Two specifics are worth naming.

**The cost of writing to the cache.** Published pricing gives two different rates depending on how long an entry is meant to live, and a single usage reading cannot tell which one a subscription meters against. The gap between them is large enough to move the absolute numbers by a fifth and small enough to leave every comparison above unchanged, which is why the comparisons are what this article states.

**The subagent break-even.** A subagent does not make a file cost "once" — it has its own conversation and re-reads its own context per turn. What it does is bound the file's cost to the subagent's turn count instead of the parent's remaining turn count, and keep the parent's per-turn floor from rising permanently. Against that, it pays a full cold start of its own. Small reads should never be delegated, because that startup dominates them. Where exactly the line falls is derived, not measured.

The mechanisms are the durable part. The API is stateless, the cache is a byte-exact prefix match, cost is proportional to content times turns, and the identity 93,786 + 261,974 = 355,760 is not a coincidence. Everything built on ratios rather than absolutes is what I would still trust after the next model ships.
