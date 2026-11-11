---
type: concept
title: Checkpoint Rewind Does Not Rewind Memory
description: >
  Rewinding or forking an agent's checkpoints restores its conversation but not its long-term memory, so the memory store needs an explicit versioning design or a rewind will leave later memories visible to the wrong branch.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 10, Short-Term Memory and Time Travel; Memory Ghost from the Past"
---

Checkpointed execution allows time travel. A developer can inspect earlier
checkpoints, fork from one with a modified value as if a chosen node had produced
it, continue from the fork, or replay from a specific checkpoint. The book presents
this as turning linear execution into a versioned state graph, which helps with
debugging reasoning, exploring alternative decisions, reproducing outputs and
auditing how a decision was reached.

The book's caveat, which it calls the memory ghost from the past, is that a rewind
restores only the conversation state. The long-term store is not rewound. Memory
written after the checkpoint is still in the store, so a fork can read facts that
were recorded on a branch that the fork no longer belongs to. The same holds for
facts written by a run that has since been replayed. The store and the checkpoints
have different histories, and nothing reconciles them automatically.

The book's advice is that the designer must choose, deliberately, whether memory
is versioned, is rolled back with the thread, or is left as it is. The source does
not prescribe one. Three designs are common options. Memory writes can carry the
checkpoint or thread identifier so that reads filter on it. Writes can be made
append-only with provenance, so a branch can ignore records from other branches.
Or the store can be rolled back by deleting what was written after the checkpoint,
which is only safe when nothing else has read those records. The first two are
options the wiki draws from the design problem, not the source's own text.

Run state is covered in [structured run state](../orchestration/structured-run-state.md), and the
store's shape is in [structured agent memory](structured-agent-memory.md). Memory
written under the wrong identity is a separate concern, covered by
[memory namespace scoping](memory-namespace-scoping.md). Keeping a full, append-only
record outside the window is the purpose of [session log outside the context window](session-log-outside-context-window.md).
