---
type: concept
title: Degradation Ladder
description: >
  Define an agent system's fallback modes in advance (full service, reduced
  tools or cheaper model, read-only, paused with a holding message, stopped)
  and classify workloads by droppability while calm, so retreat during an
  incident is chosen rather than a collapse.
evidence: weak
sources:
  - title: "Resilience Engineering in Practice"
    resource: "Pariès, ch. 2, on defence in depth and tactical retreat"
  - title: "Self-Care for Autistic People"
    resource: "Neff, ch. 3, on precommitted demand-shedding"
  - title: "Reliable Machine Learning"
    resource: "Chen et al., ch. 9, on fallback to a safe default"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 7, The Need for a Fallback Guy; Strict JSON Mode Is Not the Cure"
---

An agent system that is either fully up or fully down gives its operators, and
any agent supervising it, two choices, and both are poor during a partial
failure. Keeping full service running risks compounding damage; stopping
everything sacrifices work that could safely continue. Working out where to
land in between, in the middle of an incident, is slow and error-prone.

The pattern comes by analogy from resilience engineering. When one line of
defence is breached, a resilient system retreats to the next, lowering its
ambitions and sacrificing less-critical goals; the lines are identified
beforehand so that retreat is a chosen runbook step rather than an unplanned
collapse. Transferred to agent systems, the lines become a ladder: full
service, then a reduced tool set or cheaper model, then read-only or
answer-only, then paused with a holding message, then stopped. Work on
self-care under limited capacity adds the timing argument: demands should be
sorted green, yellow and red while headroom exists, because judging what to
drop under low capacity is itself expensive.

One software-native source supports the middle rungs. *Reliable Machine
Learning* describes switching to a simpler, bounded, non-optimal behaviour when
neither rollback nor roll-forward is available, and warns that the fallback
path must be built and exercised before it is needed. The ladder's shape and
the traffic-light classification remain analogical, so the evidence is weak.

The same logic applies to a model fallback, which the book treats as part of the
design from the start. Any model fails sometimes, through timeouts or through
output that parses but is wrong, so the fallback has to be named in advance: in
the book's words, if you don't define who the fallback is, "it's probably you at
3:00 a.m." The fallback should be tested through the whole pipeline, not only its
model call, because a fallback that struggles with the schema breaks the system
even when its answers are reasonable. Its decisions can also differ from the
primary model's, as [schema-valid decisions can diverge](../evaluation/schema-valid-decisions-can-diverge.md)
describes.

In practice each rung needs a concrete, pre-built configuration, and each
workload (channels, scheduled jobs, individual agents) needs a droppability
class decided ahead of time. Stepping down a rung then becomes a pre-approved
action inside the agent's [autonomy
envelope](../harness/envelope-bounded-autonomy.md), usually beginning with
stopping new intake, while a [hardline command
floor](../security/hardline-command-floor.md) keeps state and secrets protected
on every rung. Rungs should be rehearsed, as [fault-injection
practice](../evaluation/practise-with-fault-injection.md) does for repairs.
Repeated descents should feed [supervision-tree
escalation](supervision-tree-escalation.md) rather than oscillate silently.
