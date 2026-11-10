---
type: concept
title: Governed Tool-Call Chokepoint
description: >
  Routing every privileged tool call through one function that runs ordered policy, budget and argument checks, records the attempt before any I/O, and denies on any failure gives the harness a single place to enforce its rules.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, governed_call and BudgetTracker (Examples 6-2 and 6-3); HITL via LangGraph interrupts (Example 6-4)"
---

The book's governance design makes one function the only path by which an MCP
tool can run. It calls that function `governed_call`, and it calls the sequence of
checks inside it "a gauntlet". Each check must pass before any byte is sent. The
policy check comes first, then an allowlist check against the tools that discovery
found, then a budget check against the run's cumulative history, then argument
validation, which blocks an oversized search or a bulk delete before it executes.
The call's start is recorded before execution, so a crash leaves a trace that
the attempt began. Any failure in the logic denies the call, and no tool is
touched.

Two details matter. The record is written before the I/O, because the state of a
call whose outcome is unknown is exactly what a later recovery needs; this is the
sequencing in [the effect sandwich](../orchestration/effect-sandwich.md). And the
budget tracker subtracts the time spent waiting for a human approval from the
run's runtime, so waiting does not use up the budget. Per-tool counts and total
call counts are both live, and the stats are streamed to the run's record.

A human review step sits after the chokepoint's automatic checks. The book's
approval node blocks requests that break a hard constraint before asking a
person, passes allowed tools straight through, and interrupts only for restricted
tools, with the agent's rationale in the payload so the approver can decide on
something concrete. Interrupts are described in [human approval gates](../harness/human-approval-gates.md).

A chokepoint is only as strong as its coverage. Any path to a tool that does not
pass through it escapes every check, so the single entry point is itself a design
requirement. The book's claim is that the model cannot enforce policy, so the
boundary must be in code. The rest of the stack is in
[layered agent permission stack](layered-agent-permission-stack.md), and the
ceiling on total spend is in [session budget circuit breaker](../harness/session-budget-circuit-breaker.md).
