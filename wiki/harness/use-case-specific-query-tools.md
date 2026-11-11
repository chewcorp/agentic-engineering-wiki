---
type: concept
title: Use-Case-Specific Query Tools
description: >
  A generic query-language tool is necessary but insufficient, since agents write broad or wrong queries by trial and error, so tools that build the optimised query for a use case save cost, but granularity must stop short of fragmentation.
evidence: weak
sources:
  - title: "Observability in the AI-Native Era"
    resource: "Lipsig, Grabner and Rati, ch. 6, Generic vs Use Case-Specific Tools"
---

Every observability backend has its own query language, such as NRQL, DDSQL,
DQL or PromQL. A generic execute-query tool is needed as an escape hatch, but on
its own it is not enough. Agents start with a broad or wrong query and refine it
by trial and error, which costs time, compute and, for many backends, a fee per
query. The source's point is that the agent should not have to learn each
backend's syntax to answer a common question.

A use-case tool such as get-logs, which takes a timespan, a workload identifier
and a hostname, builds the optimised query itself. A tool such as analyse-logs
goes further and asks the platform to find problematic log patterns in one call,
so the heavy work runs inside the platform rather than in the agent's loop.

Granularity has a limit. Separate tools for logs from Kubernetes, from hosts,
from applications and from serverless functions multiply the maintenance
burden, and they give the agent more ways to pick the wrong one. The advice is
to match granularity to the top use cases. The same source adds that the more
specific the prompt about data type and timeframe, the faster and cheaper the
query, so instruction files should encode the expert's order of work, such as
metrics first and a stated timeframe. Those files are the kind of standing guidance
in [steering files as navigation pointers](../instructions/steering-files-as-navigation-pointers.md).
Compare [question-driven tool design](question-driven-tool-design.md), which
decides which tools exist, with this note, which decides how each one builds its
query. Failed queries can be caught before they reach the backend, as in
[local query validation in tool server](local-query-validation-in-tool-server.md).
