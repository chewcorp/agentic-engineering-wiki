---
type: concept
title: Session Budget Circuit Breaker
description: >
  A hard per-session cap on spend and reasoning turns, enforced in the application, stops a looping agent from running up cost, because provider spending caps are too coarse to catch one runaway session.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 7, Circuit Breaker (Example 7-3) and Table 7-4"
---

Production agents need two fuses. A **budget fuse** halts a session when its
cumulative cost passes a cap, which the book sets at fifty cents for its
example. A **turn fuse** halts it after a fixed number of reasoning steps, twelve
in the example. Tripping either raises an exception that carries the full session
state. The API layer catches it and returns a plain message that the agent is
stuck, or passes the session to a human queue. The book's loop checks the
breaker after each turn's metrics are updated, so a runaway loop stops at the
next check.

The book argues that provider-level spending caps are not granular enough to
catch one bad session, so a per-session cap in the application is the primary
defence against spend that loops without end. Its framing is that once an agent
is integrated into a production backend, "you're giving a nondeterministic actor
access to your infrastructure and your wallet." In its diagnostic runs a
breaker set at three turns tripped after repeated policy lookups and forced a
safe handoff.

A breaker is a ceiling, not a diagnosis. It works whatever the loop looks like,
which is its value, but it does not say why the loop happened. For that,
[doom-loop detection](doom-loop-detection.md) looks for repeated actions and
prompts a change of approach. Budgets count more than cost: the
[invalid action budget accounting](invalid-action-budget-accounting.md) note
counts malformed commands against the same allowance. Both sit beside the
[envelope of permitted actions](envelope-bounded-autonomy.md), which limits
what the agent may do, while the breaker limits how much it may do.
