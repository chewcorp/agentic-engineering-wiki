---
type: concept
title: Compound Mistake Amplification
description: >
  Per-step accuracy multiplies across a multi-step agent trajectory, so long
  plans fail even when each individual step looks reliable.
sources:
  - title: "AI Engineering: Building Applications With Foundation Models"
    resource: "AI Engineering (Chip Huyen), ch. 6"
  - title: "SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering"
    resource: "SWE-agent (Yang et al.), pp. 1–15 (§5.2)"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 5, Brittle JSON Trap and Reliability Math"
---

In multi-step [LLM agents](../llm-agent.md), errors compound. If each step is 95%
accurate, ten steps yield roughly 60% overall success; a hundred steps fall
near 0.6%. Long tool-using trajectories therefore demand stronger models and
tighter plan validation than single-shot generation.

Validation gates change the arithmetic. The book models a gate that catches a
failed step with probability v, so each hop's effective reliability is
p + (1 − p)·v. At p = 0.98 with no gates, a ten-agent chain succeeds about 82% of
the time. With a gate that catches 90% of failures, each hop is about 0.998 and
the ten-hop chain stays near 98%. This is a simplified model that assumes
independent steps and a fixed catch rate, so it argues for boundary checks rather
than predicting a real system's numbers. See the
[validation boundary gate](validation-boundary-gate.md).

Concrete edit loops show the same curve: any edit attempt may still succeed
(~90% eventual recovery chance in SWE-agent GPT-4 Turbo runs), but after one
failed edit recovery odds crash (~57%), and unresolved tasks are dominated by
incorrect/over-specific implementations (~52%) plus cascading failed edits
(~23% failed edit recovery). Successful recoveries usually follow ~2 consecutive
failed attempts; unsuccessful ones linger ~4–6 failures before abandoning the
edit. Successful runs finish earlier and cheaper; raising max budget rarely
rescues the long failing tail — design for early localization and
[guardrailed edits](../harness/guardrailed-edit-tool.md), not endless retries. Transition
patterns that matter for ACI design: reproduce via `create→edit→python`, then
localize (`find_file`/`search_dir`→`open`→`search_file`→`goto`), then
`edit↔python` loops; cascading `edit`×4 or `scroll_down`×4 is a smell.

This is one reason agents are higher-stakes than non-agentic use cases: tool
access makes each wrong step more damaging, and multi-step runs cost more time
and money. Design responses include shorter plans, stronger planners,
[plan-validate-execute](plan-validate-execute.md) separation,
[reflection and error correction](reflection-and-error-correction.md), and
[human approval gates](../harness/human-approval-gates.md) on irreversible actions.
