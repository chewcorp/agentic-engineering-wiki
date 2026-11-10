---
type: concept
title: Model Context Protocol
description: >
  An open standard that unifies how agents securely discover, authenticate,
  and invoke external developer tools, execution environments, and data sources.
sources:
  - title: "LLM-Based Agentic Systems for Software Engineering: Challenges and Opportunities"
    resource: "Tang & Runkler (2024), sec. 4"
  - title: "Observability in the AI-Native Era"
    resource: "Lipsig, Grabner and Rati, ch. 2, Model Context Protocol"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 5, Model Context Protocol and MCP + LangGraph"
---

Historically, agent harnesses implemented bespoke, hard-coded tool wrappers for each execution environment, database, or API. This created tight coupling between the agent's reasoning loop and specific runtime environments, leading to fragile integrations and duplicated maintenance.

The **Model Context Protocol (MCP)** provides an open, standardized client-server protocol that separates agent reasoning from external capability providers:

- **Dynamic tool discovery**: Agents query MCP servers at runtime to inspect available tools, their JSON schemas, input constraints, and descriptions, populating the [tool inventory](tool-inventory.md) on demand.
- **Contextual resources**: MCP standardizes access to external context—such as local repository files, issue trackers, documentation stores, and database schemas—via uniform URI templates.
- **Prompt templates**: MCP servers can expose server-managed prompt templates and workflows tailored to their specific underlying systems.
- **Security and authorization**: MCP establishes clear trust boundaries, allowing fine-grained permissions, sandboxing, and user approval gates before tool actions execute.

The protocol is client-server. Each client holds a connection to one server, and
a server can offer several primitives. Tools execute actions. Resources supply
context. Prompts are reusable templates. Sampling lets a server ask the host's
model for a completion without bundling a model SDK. Elicitation asks the user for
input or confirmation. Logging carries diagnostic messages, and tasks wrap
long-running work whose result is fetched later. Servers advertise their primitives
on request, so a host can gain a capability without changing its code. MCP is one
enhancement among several. It does not replace fine-tuning or retrieval.

In the book's LangGraph integration the client is stateless by default, so each tool
call opens a fresh session. A stateful session is needed only when server-side
state has to persist between calls. The book recommends stdio for local,
single-user servers and HTTP for multi-user ones, and it places policy enforcement
in the server rather than in the model's instructions.

By decoupling tool implementation from the agent harness, MCP allows autonomous software engineering agents to interact with diverse developer ecosystems without requiring custom harness code for each new service.
