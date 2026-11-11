---
type: concept
title: Agentic Cost Multiplier
description: >
  One user request in an agent expands into a conditional set of model and tool calls, so cost should be estimated as expected calls per request summed over steps rather than as a single call's price.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 11, The Agentic Cost Multiplier; Table 11-3; Deployment Topology Dominates at Scale"
---

A single user request in an agent is a conditional workflow. Routing, retrieval,
reasoning, reflection, guardrail checks and synthesis each run only when the
workflow reaches them. The book estimates the cost of a request by expected
calls per step: the probability that a step runs, multiplied by the calls it
makes when it runs, adjusted for any memoisation. The estimates are summed
across steps to give the system's multiplier. Production cost follows the
expected workload over time, not the worst case.

The book's illustrative table gives a router that always runs once (1.0), a
retrieval step triggered 80% of the time and memoised to 25% (0.6), a reasoning
step triggered 70% of the time (0.7), a reflection step triggered 25% of the
time with two calls each (0.5), and guardrails that always run twice (2.0). The
sum is 4.8 model calls per request. The numbers are illustrative, but the method
is what the book wants readers to use.

Prompt caching lowers part of the multiplier. Shared prefixes such as the system
prompt, tool definitions and examples are not reprocessed on every call. The
book's illustration: a 5,000-token shared prefix repeated across each step
amounts to 30,000 prefix tokens before generation. Caching does not remove the
calls themselves, so routing and reflection still run when the workflow needs
them. [Multi-agent token inflation](multi-agent-token-inflation.md) describes the
same problem as growth in dialogue, and [memoized planner decisions](memoized-planner-decisions.md)
describes the one lever that removes calls rather than shrinking them.

The book also argues that deployment topology matters at scale. Self-hosted and
hybrid setups add capacity in discrete steps, because GPUs are provisioned in
units, while API billing grows smoothly with use. The cheapest design is rarely
all managed API or all self-hosted, since each step can have a different cost
curve. Measure cost per request and per successful task, as in
[LLM token and cost metrics](../llm-token-and-cost-metrics.md) and
[successes per million tokens](../evaluation/successes-per-million-tokens.md).
The evidence here is a worked illustration, not a measurement.
