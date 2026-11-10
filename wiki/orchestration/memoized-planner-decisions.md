---
type: concept
title: Memoized Planner Decisions
description: >
  Caching an agent's planner decisions under a key that covers the full conditioned state lets repeated situations reuse a past decision, but a high hit rate can also mean the agent is stuck.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 11, Memoizing Agentic State Transitions; Operational Cautions; Impact"
---

Classic memoisation caches intermediate values. The book extends it to planner
decisions: record how the agent acted in a given state and reuse that decision
when the same state recurs. Framed as a state machine over facts, the planner
maps an agent state to its next action with an LLM call that grows with the
context. A cache lookup costs a constant amount, so decisions repeated across
runs stop paying for the planner.

The key must cover everything the decision depended on. The book's planner
context includes the task, the current world view as a frozen and order-
independent set of facts with their evidence, the tools available, and the tools
that are stale. Keying on the task and facts alone is unsafe. A decision made
under pressure from stale tools could replay in a context with no stale tools
and bypass the signal that stopped a loop. Invalid or failed planner outputs
must never be stored, or the cache learns the wrong behaviour.

Exact matching is often too strict, so the book uses three tiers: an exact hash
of the raw facts, a hash of normalised facts that tolerates formatting
differences, and an embedding similarity match above a threshold, defaulting to
0.90 cosine similarity. A semantic candidate is reused only when its tool and
stale-tool context matches. The similarity threshold trades cost for safety: too
low and the agent reuses a decision for a task that is only vaguely similar. In
a changing environment, the book adds a time-to-live, because a decision that was
right five minutes ago may no longer be right.

The sharpest caution is about hit rate. A 90% hit rate in which every hit leads
nowhere means the agent is looping on a stale decision: "you're not saving money,
you're just failing faster." Track whether cached decisions produce new state
changes, and force a live planner call, a different tool, or completion when
hits stop making progress. The book's figure of 47% of calls avoided comes from
an illustrative run, not a measured benchmark, which is why the evidence is
weak. Compare this with [response caching](../response-caching.md), which reuses
model outputs, and see [doom-loop detection](../harness/doom-loop-detection.md)
for the loop this can hide. The cost picture is in
[agentic cost multiplier](agentic-cost-multiplier.md).
