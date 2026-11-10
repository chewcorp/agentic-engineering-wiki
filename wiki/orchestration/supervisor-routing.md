---
type: concept
title: Supervisor Routing
description: >
  A supervisor agent that picks which specialist acts next gives a small multi-agent team strong control and avoids deadlock, at the cost of a central bottleneck.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 2, Advanced Agent Paradigms: Supervisor and Hierarchical Agents; Table 2-4"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 4, Table 4-5 and Putting It All Together"
---

In a supervisor architecture every specialist reports to one supervisor, which
decides who acts next. Each worker returns control to the supervisor when it
finishes. The routing decision is usually a structured output that picks one
worker from a member list or a finish option. A reusable helper can build that
router for any set of workers, and a worker helper can wrap a tool-using agent
so that its last message goes back to the supervisor.

The book's trade-off summary: supervision suits small teams with clear task
boundaries, is simple to set up, gives strong control and avoids deadlocks. Its
costs are a central bottleneck and a single point of failure. Each routing step
is an extra model call, and the quality of that one decision bounds what the
whole team can do. Keep the supervisor's job narrow, to choose a worker or
finish, and let the workers do the work. The book's research team follows this
shape: a supervisor on a deliberate model routes among fast specialists and
decides when the outcome is final. The specialists' prompts are narrow: the
analyst, for example, must call one tool exactly once and return only its result.

Supervisor routing organises work across agents. It is different from
[tree of thoughts](../reasoning/tree-of-thoughts.md), which branches to explore
alternative reasoning for one task. When one supervisor's team has to contain
sub-teams, see [hierarchical supervision](hierarchical-supervision.md). When
control should pass directly between peers, see
[swarm handoff coordination](swarm-handoff-coordination.md). Any worker that
writes shared state needs the rule in [single-threaded writes](single-threaded-writes.md).
For the broader role pattern, see [role-based agent delegation](role-based-agent-delegation.md).
