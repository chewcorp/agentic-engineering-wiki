---
type: concept
title: Memory Hacking
description: >
  Adversarial conversation that implants false past events into an agent’s
  durable memory — a retrieval/reflection risk beyond ordinary prompt injection.
sources:
  - title: "Generative Agents: Interactive Simulacra of Human Behavior"
    resource: "Generative Agents (Park et al.), pp. 1–22"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 10, Memory Poisoning Threat Model (citing Dash et al., 2026 preprint)"
---

Long-lived agents that store comprehensive experience in a
[memory stream](../knowledge/memory-stream.md) can be attacked by **memory hacking**: craft
dialogue that convinces the agent a nonexistent past event occurred, then let
retrieval and [reflection trees](../knowledge/reflection-tree.md) amplify the lie into
plans and speech. This is distinct from one-shot
[jailbreaking and prompt injection](jailbreaking-and-prompt-injection.md) —
the payload persists across sessions via the memory architecture itself.

Mitigations for [generative agent architecture](../knowledge/generative-agent-architecture.md)
and [OS-inspired agent memory](../knowledge/os-inspired-agent-memory.md): provenance tags on
observations, distrust unverified user claims before archival write, audit
logs of inputs and generated outputs, and disclose computational nature to
limit parasocial misuse. Treat embellished or world-knowledge-bleed memories
as expected failure modes in
[offline prompt evaluation](../evaluation/offline-prompt-evaluation.md) interviews, not as
rare bugs.

Poisoning is the same risk with the attack moved into stored state. A 2026
preprint reports that untrusted content written to agent memory later returns as
trusted context, that agents which write and retrieve memory aggressively are
easier to exploit, and that prompt-injection defences do not cover this path
because nothing is injected into the prompt. The control point is the write:
see [validate before durable memory write](../knowledge/validate-before-durable-memory-write.md),
and [memory namespace scoping](../knowledge/memory-namespace-scoping.md) for which
records a given user's write can reach.
