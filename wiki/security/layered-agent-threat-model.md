---
type: concept
title: Layered Agent Threat Model
description: >
  Threat modelling an agent means tracing how a failure in one layer, such as the model, orchestration, tools or memory, propagates into the others, because the agent's own reasoning creates paths that a classical component model lacks.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 12, Layered Threat Model (Table 12-1); CIA Mapping (Table 12-2); Risk Categories (Table 12-3); Indirect Prompt Injection Propagation (Fig. 12-4)"
  - title: "OWASP Threat Defense COMPASS"
    resource: "Cited in AI Agents (Koenigstein), ch. 12, Threat Modeling References"
---

The book's threat model has six layers. The organisation layer holds the policies,
access control and role assignments that apply before the agent runs. The model
layer is where instructions are followed or manipulated. The orchestration layer
holds planning, routing, retries and handoffs, and it is where failures are
amplified. The tool layer connects the agent to real actions and data and carries
the highest impact. The memory layer holds short-term state, persistence and
retrieved context, so a failure can outlive the interaction. The infrastructure
layer holds compute, networking and deployment, and weakness there undermines
isolation and availability.

Each layer maps to the classic security properties in a different way. The model
mainly threatens integrity, because a distorted reading of intent distorts every
decision after it. Orchestration threatens integrity and availability, since bad
plans and loops consume resources. Tools threaten confidentiality, integrity and
availability at once. Memory threatens confidentiality and integrity, because
stored state can leak or bias later behaviour.

The book's central claim is about propagation. A classical system exposes a
vulnerable interface, but an agent can reason its way into misusing a legitimate
one. Its worked example is an indirect injection hidden in a retrieved review. The
model's goal is rewritten, the orchestrator plans around the rewritten goal, the
tool call passes every per-tool check because the caller is authorised, and the
memory records the request as a success. The compromise is upstream and the
planner has no basis for suspicion. The risk categories follow the same pattern:
operational faults, adversarial manipulation, agent-specific failures such as
cascades and loops, and systemic effects at scale. Some of these need no attacker,
since a system working as designed can reason wrongly.

The book cites the OWASP COMPASS framework as a lifecycle method. The source says
the framework does not define the unit of analysis, and the propagation view is
its proposed fill. The design takeaway is that failures cannot all be predicted,
so a system should be built so that failures are observable, their impact is
bounded, and they do not persist. The pieces are in [indirect prompt injection](indirect-prompt-injection.md),
[memory hacking](memory-hacking.md), [agent system-level defenses](agent-system-level-defenses.md),
and the [agentic red-teaming loop](agentic-red-teaming-loop.md).
