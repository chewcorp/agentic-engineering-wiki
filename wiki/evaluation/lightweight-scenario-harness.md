---
type: concept
title: Lightweight Scenario Harness
description: >
  A small harness that runs typed scenarios concurrently, validates outputs against a schema before scoring, and combines cheap rule checks with a judge's score is enough to put the worst cases in front of experts.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 8, Behavioral Stress Testing (harness pattern and scoring); Ticket triage example"
---

The book builds a harness that is deliberately small, not a platform. Its backbone
is typed scenario and output schemas, written as Pydantic models, so every test
case and every result has the same shape. Each stress dimension is translated into
a rule check. Urgency maps to a classification marker, ambiguity to a
clarification marker, frustration to an empathy marker, knowledge mismatch to an
explanation marker, and adversarial intent to a boundary marker, with bypass
markers counted against the agent. The book calls these early signals rather than
judgments of quality.

Scenarios run concurrently with a semaphore that caps how many run at once.
Outputs are validated against the schema before they are scored. Scoring has two
phases: the deterministic checks, and a judge model whose score is used to set
review priority, not as ground truth. The book combines them with weights, in its
example 0.4 for the deterministic score and 0.6 for the judge's. Every run exports
its traces in a structured form, with the input, the prompt that generated the
output, the raw and parsed output, the scores and the timing, as JSON or CSV for
review in a spreadsheet or dashboard.

A review view that puts the highest-risk outputs first lets domain experts work
through what matters, and the deterministic artefacts make a run reproducible. The
harness sits in a loop: generate the scenarios, run them, analyse the failures,
change prompts, tools or policies, and repeat until the results stop moving. The
weighting is the book's choice, and its value is not established. A judge score
that orders a review queue is useful, but a judge can be wrong, so its
disagreement with the rules is a thing to inspect, as in
[grader correctness as eval hygiene](grader-correctness-as-eval-hygiene.md). The
scenarios it runs come from [stress-dimension scenarios](stress-dimension-scenarios.md).
For the grader types it combines, see [agent grader types](agent-grader-types.md).
