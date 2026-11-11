---
type: concept
title: Hierarchical State Machine Control
description: >
  A hierarchical state machine lets a group of states inherit one set of guards and policies, so safety rules stay consistent as an agent's modes multiply.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 1, Finite and Hierarchical State Machines; Mapping FSM/HSM to Agent Frameworks"
---

A hierarchical state machine lets states contain states. A **superstate**
groups child states and their common policy; its shared entry and exit logic
runs once rather than once per child. A **substate** is a concrete mode inside
a superstate, with its own edges, and it inherits the superstate's guards and
actions. A **history** marker remembers where the machine was. Shallow history
resumes at the last active child, and deep history resumes inside nested
grandchildren, which makes it a checkpoint for part of the graph. A **parallel
region** runs two or more child regions independently, which suits fanning out
tool calls or agent roles and joining them afterwards.

The practical payoff is where policy sits. Attach a rate limit, a safety filter
or a circuit breaker to a WORKING superstate that contains PLAN, ACT and REFLECT
substates, and all three inherit it. A router defined once can be reused by
every node that needs it, instead of being copied into each. The source's
research-agent example is a sub-machine of search keywords, gather URLs, scrape
data and summarise findings, where each state can be mapped to a dedicated
agent. The book presents this as a design argument; it reports no measured
effect on reliability.

In LangGraph a subgraph plays the role of a superstate and its nodes play the
substates. The parent's shared state is the interface between them. Parent-level
guards enforce global limits such as budget or safety, while child guards choose
among the substates. Because guards are plain code, they are deterministic and
auditable, which is the property [finite state machine control](finite-state-machine-control.md)
relies on. Hierarchy changes who decides, not what can happen: the
[envelope of permitted actions](../harness/envelope-bounded-autonomy.md) still
bounds every transition. For the state a hierarchy carries between steps, see
[structured run state](structured-run-state.md).
