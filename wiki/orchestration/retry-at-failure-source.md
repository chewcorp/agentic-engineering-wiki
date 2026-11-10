---
type: concept
title: Retry at the Failure Source
description: >
  Retry a bad model output inside the generation call that produced it and reserve graph-level retries for boundary failures and infrastructure faults, so each failure is handled where it arises.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 5, Instructor: Moving Recovery Closer to the Source; Two correction loops; Table 5-3"
---

The book separates two correction loops in a validated agent. The inner loop
belongs to the structured-output library. It is high frequency and low latency,
and it handles model-specific faults such as a hallucinated key or a missing
field. The library re-asks the model with the validation error attached, up to a
bounded number of attempts, before the output reaches application logic. The
outer loop belongs to the orchestration layer. It is lower frequency and higher
latency, and it handles system faults such as a policy change, a database
constraint or a logic error, by routing the failing node back for another
attempt.

The retry semantics differ by layer. A boundary gate runs after generation, at
node or subgraph granularity. It may need to rerun generation nodes, which is
costly for tool outputs, persistence and handoffs. A generation-level retry
happens before the boundary, covers one model call, and avoids rerunning
upstream nodes. Neither layer handles timeouts or infrastructure failures well.
The book says those remain the job of the graph runtime, through its retries and
checkpoints, and that the generation-level retry does not remove the need for
them. A checkpointer also preserves inner-loop progress when the outer loop
fails, so the two loops compose.

The reason to keep the layers apart is cost and diagnosis. A model's formatting
slip retried in place costs one call. The same slip sent back through the graph
can rerun several nodes and hide the cause behind a later failure. A bounded
retry at each source keeps failures local, which is the goal of the
[validation boundary gate](validation-boundary-gate.md). Retries still need a
ceiling, as in [supervision-tree escalation](supervision-tree-escalation.md),
and repeated failures on the same item call for a changed approach rather than
more retries, as in [doom-loop detection](../harness/doom-loop-detection.md).
