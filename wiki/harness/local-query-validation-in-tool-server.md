---
type: concept
title: Local Query Validation in Tool Server
description: >
  Checking a query's syntax and permissions inside the tool server gives the agent an immediate, specific error without a backend round trip, and the server's own failure counts show whether the check is worth keeping.
evidence: weak
sources:
  - title: "Observability in the AI-Native Era"
    resource: "Lipsig, Grabner and Rati, ch. 6, Tip: Local Versus Remote Validation"
---

An agent's first queries often fail for two reasons: the syntax is wrong, as when
a relative time such as "yesterday" is written differently in each query
language, or the account lacks permission for the query. Each failure costs a
backend round trip, a charge where the backend bills per query, and another turn
for the agent. The source recommends checking both in the MCP server before
anything reaches the backend. Basic syntax checks and permission checks return
an immediate error, and the agent can correct itself on the next turn.

The error should be specific enough for the agent to act on. An error that says
the time expression is not valid in this query language, and gives the accepted
form, is more useful than a generic rejection. This is the same principle as
[failures returned as actionable feedback](failures-returned-as-actionable-feedback.md).

The source also says to instrument the server, counting failed tool executions
by cause: incorrect query, or insufficient permission. Those counts are the
evidence for adding a local check and for choosing which check to add first.
Local validation is a fast filter. The backend still enforces its own rules,
and the server's check should be read as a convenience for the agent rather
than as the access control itself. The validated queries are built by
[use-case-specific query tools](use-case-specific-query-tools.md).
