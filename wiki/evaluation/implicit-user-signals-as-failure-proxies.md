---
type: concept
title: Implicit User Signals as Failure Proxies
description: >
  Retries, rephrasing, corrections, abandonment and manual fallbacks reveal production failures that users never rate, so they are a rich but indirect source of failure evidence that needs interpreting before it is trusted.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 9, Don't Depend on User Feedback (Table 9-3); Sensitive Operations and Data Retention"
---

Explicit feedback is sparse, and it is biased toward the extreme cases that users
bother to rate. Most users abandon the conversation, rephrase, retry or correct
the agent without rating anything. The book's position is that these behaviours
are the most available failure signal, but each is indirect, and each has more than
one possible cause. Interpreting them requires care.

The book's mapping from signal to likely cause is as follows. Retries and
rephrasing suggest the response was unclear, incomplete or aimed at the wrong
thing. A user correction suggests an error, missing context or faulty reasoning.
Abandonment suggests the answer was not useful, too complicated, or that the
task was not addressed. An immediate follow-up for clarification suggests the
answer lacked specificity. Bypassing the agent for a manual route suggests it is
not trusted. Acceptance without any edit looks like success but does not guarantee
that the answer was correct, which is why it is the weakest of the signals.

The signals are a way to choose traces for review, not a verdict. A cluster of
retries on one workflow is a good place to start reading, following the
[error analysis first](error-analysis-first.md) practice of reading real traces
before automating a judge. Confirmed failures are then promoted into benchmark
cases. Trace-level symptoms complement the user signals: repeated tool calls point
to unstable tool selection, invalid structured output to formatting instability,
latency and cost spikes to wasteful reasoning or prompts, and hallucinated claims
to missing grounding. Connecting those signals back to the offline suite is the
loop described in [eval monitoring feedback loop](eval-monitoring-feedback-loop.md).

Collecting these traces has a cost. Tracing can expose personal or confidential
data, so the book says to disable it for workflows that handle personal data,
credentials or confidential material, to honour the retention terms that apply,
and to separate traces by tenant where needed. Once a failure is confirmed, the
[root-cause step localisation](root-cause-step-localisation.md) note explains
how to find the step where it began.
