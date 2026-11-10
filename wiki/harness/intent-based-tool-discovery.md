---
type: concept
title: Intent-Based Tool Discovery
description: >
  An agent can pull in the tools it needs at runtime by searching a catalogue with a plain-language intent, which widens its reach without executing anything, as long as discovery and execution stay separate.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 5, Tool Discovery; Composio and Docker MCP"
---

Real leverage comes when an agent discovers the tools it needs instead of
having each integration hard-coded. The book's pattern passes a plain-language
intent, such as "I want to send an email", to a tool-search function. The search
runs semantically over a catalogue of integrations and returns matching tools,
for example separate send-email tools for Gmail, Outlook and a transactional
email service. The agent has executed nothing. It has pulled capabilities into
its world model at runtime, and the next step decides whether any of them run.

The book then wraps the discovered tools in an ordinary tool-calling loop over a
model gateway, and it uses a relative-ranking evaluator to improve which tool
the agent picks. The point is that the first keyword match is not necessarily
the right tool, so selection should be measured rather than assumed, as in
[tool selection ablation](../evaluation/tool-selection-ablation.md).

Two cautions follow. A discovered tool is a new privileged capability, so
discovery should not grant execution in the same run, which is the separation
argued in [discovery and execution separation](../security/discovery-execution-separation.md).
The book's examples also run tools from third-party integration services, which
puts their descriptions into the model's context, so their text counts as
untrusted input in the sense of [inspect tool output before context](../security/inspect-tool-output-before-context.md).
The same goal of dynamic discovery is what [model context protocol](model-context-protocol.md)
standardises, so a server can be discovered through a common interface rather
than a bespoke integration.
