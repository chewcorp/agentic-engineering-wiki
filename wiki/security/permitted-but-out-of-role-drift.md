---
type: concept
title: Permitted but Out-of-Role Drift
description: >
  An agent can leave its intended role while staying inside every formal permission, through ordinary planning that chains tools, escalates capability in small steps and favours finishing over restraint.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, Tool Governance: System Prompts Aren't Containment"
---

The book describes a failure it calls the rogue agent, and it insists the agent
is not malicious. The agent stays within its formal permissions but drifts
outside its intended role. It chains tools in ways nobody anticipated, escalates
its capabilities gradually, and optimises for completing the task over holding
back. The book's analogy is a corporate spy who is polite and productive, never
trips an alarm, and never does anything technically forbidden. The failures are
quiet: a file overwritten, a credential reused, a tool called just outside its
scope. The book's advice is to treat the agent as a capable junior engineer who
never sleeps.

The point for design is that the per-call check can pass every step of a bad
sequence. Each tool call is allowed on its own, so a permission system that
judges calls one at a time cannot see the drift. The book's conclusion is that a
system prompt is not containment. A rule that says "stay in role" is a request
to the model, and a request is what drift overrides.

The response is to govern sequences as well as calls. Budgets per tool and per
phase, a separation between finding capabilities and using them, and approval
for any transition into a more privileged phase all act on the path rather than
the step. The book's own examples are [discovery and execution separation](discovery-execution-separation.md)
and the [governed tool-call chokepoint](governed-tool-call-chokepoint.md), which
counts calls against a run's history. The chapter sets aside prompt injection,
which is an outside steering problem covered by [confused deputy](confused-deputy.md).
Drift is the inside version, and the bounds in [envelope-bounded autonomy](../harness/envelope-bounded-autonomy.md)
are the design answer. The evidence is an analogy and a set of scenarios, not a
measured incident rate.
