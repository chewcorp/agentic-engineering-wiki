---
type: concept
title: Inter-Agent Coordination Protocols
description: >
  Structure multi-agent interaction through formal task bidding, contract awards,
  and standardized messaging layers rather than free-form conversational chatter.
sources:
  - title: "LLM-Based Agentic Systems for Software Engineering: Challenges and Opportunities"
    resource: "Tang & Runkler (2024), sec. 4"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, A2A Governance Primitives (Table 6-1)"
---

When scaling [multi-agent architecture](multi-agent-architecture.md), allowing agents to interact purely through unstructured conversational chat leads to [multi-agent token inflation](multi-agent-token-inflation.md), ambiguous task handoffs, and cascading coordination failures.

**Inter-agent coordination protocols** establish formal interaction contracts across multi-agent networks:

- **Contract Net Protocol (CNP)**: Decomposes task allocation into a structured three-phase market:
  1. *Task announcement*: A manager agent advertises an engineering task (e.g., "generate unit tests for module X") along with constraints and budget.
  2. *Bidding*: Contractor agents evaluate their capabilities, availability, and tool access to submit formal bids.
  3. *Awarding and execution*: The manager awards the contract to the best-suited agent, which executes the work and returns structured deliverables.
- **Agent-to-Agent (A2A) and Agent Network Protocols (ANP)**: Standardized network and messaging envelopes that support agent identity verification, service discovery, typed payload routing, and asynchronous status notifications.

The book also uses A2A as a governance layer rather than only a messaging standard.
Three of its primitives serve that purpose. Artifacts make an agent's intent,
approvals and decisions reviewable records. Task states mark work as active, paused
or blocked pending approval. Separating the reasoning channel from the execution
channel lets a governance layer decide which signals pass, which limits the damage
from one bad decision. The book's argument is that the rules then hold structurally,
rather than depending on a prompt asking the agent to comply. The governance side is
covered in [discovery and execution separation](../security/discovery-execution-separation.md).

Replacing open-ended chat with explicit delegation protocols provides inspectable lifecycle flow, bounds token overhead, and enables dynamic agent discovery in complex enterprise environments.
