---
type: concept
title: Programmatic Tool Calling
description: >
  Code mode lets the model write one short program over approved tools so that loops, filters and aggregation run in a sandbox and only the final result returns to context, which saves round trips but makes injected text executable.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, Safeguarding Programmatic Tool Calling; Governed Code-Mode Workflow"
  - title: "Programmatic Tool Calling"
    resource: "Anthropic, cited in AI Agents (Koenigstein), ch. 6 (reference list)"
  - title: "Code Execution with MCP"
    resource: "Anthropic, cited in AI Agents (Koenigstein), ch. 6 (reference list)"
---

Sequential tool calling makes structured work expensive. Each tool result
re-enters the model's context, and each next action needs another model turn.
Code mode changes the unit of action. The model writes a short program that
calls approved tools as functions, using ordinary control flow such as loops,
conditionals, filtering, sorting and aggregation, and the program runs in a
sandboxed interpreter. Only the final result goes back to the model. Intermediate
results stay in the interpreter, so the context does not grow with each call.

The book reports one instrumented run in which 16 tool calls (eight cities,
two tools each) took zero model round trips. Structured work stays structured:
a loop is a loop and a ranking is a sort. Latency and token cost fall for the
same reason, since the model is consulted once rather than sixteen times.

The security effect is easy to overstate. Poisoned intermediate results that
never enter the context cannot directly steer the next reasoning step, which is
a real reduction in one channel. But the risk boundary moves. The system now
executes model-generated code, and an injected instruction that would have
produced one inspectable structured call can become a program that reads
environment variables, opens files or sends data to an outside endpoint. The
book's summary is that the efficiency win is a reason to use code mode, not a
reason to skip the sandbox.

The book's governed form sets the contract in the system prompt. The model may
call only the documented host functions, may write plain Python with no imports,
file, network or operating-system access, and must end with an expression that
gives the answer. The tool descriptions shown to the model are the governed
surface. Code mode is different from [function-call chaining](function-call-chaining.md),
which still takes one model turn per step. The isolation needed to make it safe
is covered in [capability-scoped interpreter](../security/capability-scoped-interpreter.md),
and the host-side gate that decides which calls may run is in
[governed tool-call chokepoint](../security/governed-tool-call-chokepoint.md).
