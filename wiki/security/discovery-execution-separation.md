---
type: concept
title: Discovery and Execution Separation
description: >
  Keeping tool discovery and tool execution in different runs stops a single run from finding a capability and immediately using it, which is the pivot that turns ordinary planning into escalation.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, Tool Governance: System Prompts Aren't Containment; GovernancePolicy (Example 6-1)"
---

Discovering a tool widens what an agent can reach. A planner that has just found
a tool has a plausible reason to try it, and that is how an agent with ordinary
goals can move outside its role. The book's governance policy addresses this
with a separation constraint: discovery and execution cannot happen in the same
run. The rogue pattern it targets is discovery followed at once by execution,
and the policy breaks it structurally rather than by asking the model to behave.

The same policy holds two related settings. A discovery-only policy permits
searching the catalogue without calling anything. A connection flag separately
controls whether a run may create a new connection, and a second flag forbids a
run from creating a connection and executing through it. The policy is
default-deny, so any tool not matched by an explicit allow pattern is blocked.

The result is that a discovered tool has to pass through a decision point before
it can run. In the book's design that point is a later run, which starts with the
discovered list as its input and checks each call against an allowlist derived
from discovery. A human or a policy can then review the list before execution.
The cost is latency, and a second run has to rebuild context from the artefacts
the first run wrote, so the discovered list must be stored as state rather than
left in the first run's conversation.

This separation is one of the rules in [layered agent permission stack](layered-agent-permission-stack.md)
that act on sequences rather than single calls, which is the concern of
[permitted but out-of-role drift](permitted-but-out-of-role-drift.md). The
discovery side is described in [intent-based tool discovery](../harness/intent-based-tool-discovery.md),
and the call-time gate that enforces the allowlist is the
[governed tool-call chokepoint](governed-tool-call-chokepoint.md).
