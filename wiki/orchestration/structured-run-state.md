---
type: concept
title: Structured Run State
description: >
  A workflow's state should be a typed snapshot of routing, tool results and checkpoints kept apart from message text, so a run can be resumed, branched and audited.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 1, Giving the LLM a State (LangGraph); ch. 5, Checkpointing as Fault Tolerance; ch. 7, Serializable Agent State"
---

The state of an agent run is the structured runtime snapshot of the workflow,
not the text passed to the model. It can hold routing information, tool
results, checkpoints and branch identifiers. Message history is one field among
these. In LangGraph the state is a typed shared structure, nodes read it and
return updates, and edges pick the next node from it. A node can return its
state update and name the next node in one step.

Only the fields a recovery needs must be serialisable. One book example keeps a
session identifier for tracing, the model and system prompt, the rolling
history, a turn count for loop detection, token and cost totals for budgets, a
status with a halt reason, and a checkpoint count. If the process dies, the
record reloads and the run continues. A checkpoint should hold more than
messages: intermediate tool results, the step that was paused, pending routing,
and the configuration needed to resume with the same model, prompt, tools and
policy. Without those, a resumed run can repeat an expensive upstream call or
quietly run under different rules. The book's example is a crash during an
image step after an expensive reasoning model call, where the draft should not be
paid for again.

Threads separate branches. A checkpointer keyed by a thread identifier restores
that thread's messages on each turn, so the agent reuses earlier results without
repeating them. A new identifier starts an isolated branch that diverges while
the main thread stays intact. Checkpoints can contain sensitive context and tool
output, so the book attaches an encrypted serializer at rest.

Run state is not durable memory. Checkpoints restore the conversation, not the
long-term store, which is the caveat in
[checkpoint rewind does not rewind memory](../knowledge/checkpoint-rewind-does-not-rewind-memory.md).
Recovery semantics for effects that may have happened already are in
[journal then replay](journal-then-replay-durable-execution.md) and
[unknown state on resurrection](../harness/unknown-state-on-resurrection.md).
For what the machine does with this state, see
[finite state machine control](finite-state-machine-control.md).
