---
type: concept
title: Agentic Red-Teaming Loop
description: >
  Red-teaming an agent means running adversarial scenarios through full execution paths, judging the trace of planning, tool calls and memory writes, and feeding the findings back into prompts, orchestration and tool constraints.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 12, Red Teaming (Fig. 12-5); ch. 8, Early Threat Testing: Hardening Agents During Development"
  - title: "OWASP Top 10 for Agentic Applications"
    resource: "Cited in AI Agents (Koenigstein), ch. 8, ASI01 to ASI10 threat list"
---

The book treats red-teaming as an iterative cycle, not a list of prompts to try.
Its five steps are: define the risk surface from the threat model, covering
injection, data exfiltration, policy bypass, unintended tool use and memory
corruption; simulate adversarial conditions such as injected instructions,
jailbreaks, manipulated context and misuse of external data; execute against the
system under realistic conditions and evaluate full paths, meaning planning, tool
calls and memory updates together, not isolated prompts; analyse the results
against each risk, looking for compromise of confidentiality, integrity or
availability at any layer rather than only explicit policy violations; and feed
the findings back into prompts, orchestration logic, tool constraints and memory
handling.

The book uses the OWASP Top 10 for agentic applications as a threat taxonomy. Its
entries include goal hijack through hidden instructions, tool misuse through unsafe
composition or runaway calls, identity and privilege abuse, supply-chain
compromise of tools and schemas, unexpected code execution, memory and context
poisoning, cascading failures and rogue agents. Automated red-teaming frameworks
make the loop repeatable, and the book's example uses one that supports single-
and multi-turn scenarios.

The judging step is where agentic testing differs from chat testing. A
final answer can look safe while the trace shows the forbidden tool was called,
so the evaluation should read executed and blocked tool calls rather than the
text alone, as in [outcome versus transcript](../evaluation/agent-eval-outcome-vs-transcript.md).
The book also argues for starting early. Its dual-track design runs behavioural
stress testing and threat testing while the prototype is still being built, because
adding security late bakes architectural weaknesses into the system. Which attacks
to try depends on the interaction pattern, whether the system is single-turn or
stateful, whether it generates content or acts, and what it can reach. Adaptive
attacks can defeat published defences, as in [injection defences are bypassable](prompt-injection-defences-are-bypassable.md),
so a red-team pass shows coverage rather than proof. The threat model that sets
the risk surface is [layered agent threat model](layered-agent-threat-model.md).
