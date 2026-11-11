---
type: concept
title: Subagent Persistence Modes
description: >
  A subagent can be per-invocation, per-thread or stateless, and the choice decides whether it remembers earlier calls and whether it can pause for input or recover after a crash.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 10, Memory Topology in Multi-Agent Systems; Persistence Modes for Specialists (Table 10-6)"
---

When an orchestrator calls a specialist agent, the specialist's memory must be
chosen deliberately. The book names three persistence modes. A **per-invocation**
specialist keeps nothing between calls and behaves like a stateless function. A
**per-thread** specialist accumulates its own history across calls, so it behaves
like a persistent colleague. A **stateless** specialist keeps no state at all,
which removes checkpoint-based capabilities: it cannot wait mid-run for user
input and has no durable recovery.

The orchestrator should pass only the current turn to a specialist. Forwarding
the full parent history injects noise and undoes the isolation that justified
delegating in the first place. A per-thread specialist then reconstructs its
context from its own message history. In the book's example, only the per-thread
billing specialist accumulates history across turns, while the other two modes
start fresh each time.

The choice follows from what the specialist must do. A specialist that answers
one kind of question is cheapest per invocation. One that works through a
multi-turn case with a customer needs a thread, and a specialist that may pause
for approval or survive a restart needs checkpoint support, which rules out the
stateless mode. In LangGraph, the book notes that `checkpointer=True` is valid
only for nested subgraphs, not root graphs, where it raises an error, so a
persistent specialist is built as a subgraph when it relies on that setting.

The same mode choice recurs across the wiki's delegation patterns. A
[stateful task agent](../harness/stateful-task-agents.md) is bound to one work
item for its whole life, which is the per-thread case. Parallel writers each
[work in their own worktree](worktree-isolated-subagent-fanout.md), which keeps
their state apart. The memory topology that goes with these modes, local,
shared or hybrid, is the design question that comes first; see
[role-based agent delegation](role-based-agent-delegation.md) for the roles
themselves.
