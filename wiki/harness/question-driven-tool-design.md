---
type: concept
title: Question-Driven Tool Design
description: >
  Designing an agent's tools by first listing the questions its users will ask, then building the tool and parameters each question needs, produces a tool set that fits real use better than one copied from a platform's API.
evidence: weak
sources:
  - title: "Observability in the AI-Native Era"
    resource: "Lipsig, Grabner and Rati, ch. 6, Defining Tools: Start with the Questions"
---

A practitioner who built an early open-source observability server recommends
starting from the engineer's side. Ask what they will prompt, what an
observability expert would check, and make that queryable. The first question
in the example was which problems exist in the application, which became a
list-problems tool over open alerts. The next request, "show me problems from
yesterday", forced a timeframe parameter. The tool set grows from questions, and
its parameters come from follow-up questions.

Vendors ship observability servers whose tools mirror their own vocabulary,
such as problems, incidents, dashboards, logs, traces and entities. The source
notes that enterprises often start with a vendor's server and then write their
own. The reasons are control over which tools exist, and a platform-independent
tool set that keeps working when the backend changes or several platforms are in
use. In that setup the server acts as a transparent proxy to the backend.

This is the opposite of the advice for coding agents, which favours
[a minimal, general tool surface](minimal-general-tool-surface.md). The domains
differ: a coding agent composes general actions, while an operations agent
needs domain questions answered in one call. Question-driven design fits
[use-case-specific query tools](use-case-specific-query-tools.md), and the
resulting set still needs the discipline of [tool definition design](tool-definition-design.md)
and [narrow action verb sets](narrow-action-verb-set.md) for any tool that writes.
