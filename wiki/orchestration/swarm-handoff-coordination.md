---
type: concept
title: Swarm Handoff Coordination
description: >
  In a swarm, agents pass control to one another through handoff tools and the system remembers the last active agent, which keeps collaboration fluid but makes the path less predictable.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 2, Developing a Swarm of Agents; Table 2-4"
---

A swarm has no central router. Each agent holds a handoff tool that transfers
control to a peer whose specialty fits the next step, and the system tracks the
last active agent so that the conversation continues with it. LangGraph's
prebuilt handoff tool can be renamed, re-described, given task-description
arguments, and filtered for the context it carries. By default the full message
history passes across, with a confirmation message recorded as a tool result.

The book's example pairs a research assistant, which has search and scraping
tools and a handoff to the writer, with a writer that synthesises a cited
answer and hands back when the evidence is thin. The research assistant hands
off after three to five solid sources. The default active agent is set
explicitly. A demonstration on a fintech licensing question produced a three-
bullet summary with references in about thirty seconds, including one return
handoff when sources were thin.

The book's trade-off summary is that a swarm gives flexible, natural back-and-
forth, and that it is less predictable and needs robust handoff design. Each
handoff is a contract with two parts. The first is what context transfers:
passing the full history inflates tokens, which
[multi-agent token inflation](multi-agent-token-inflation.md) describes, and
filtering it can drop facts the receiver needs. The second is when control may
return. Return handoffs are how a swarm recovers from thin evidence, but without
a bound two agents can pass work back and forth, which the
[closed-loop critique failure modes](closed-loop-critique-failure-modes.md) note
describes. Compare this with [supervisor routing](supervisor-routing.md), where
one agent makes every routing decision.
