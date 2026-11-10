---
type: concept
title: Schema-Valid Decisions Can Diverge
description: >
  Output that passes a schema check on every model can still yield different operational decisions across models and runs, so format checks say nothing about whether the decisions are stable.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 7, Empirical Drift Results (Tables 7-6 and 7-7); Strict JSON Mode Is Not the Cure"
---

The book triages one support ticket with four models through a single gateway,
using the same schema, prompt and retry policy. Every model returned valid JSON.
Three gave the same answer on every run, but two of them chose priority p0 and one
chose p1. The fourth needed the degraded canonicalisation path, flipped between p0
and p1 from run to run, and kept the same sentiment label throughout. The book's phrase for this is that "these failures don't
look like failures at all". Each answer was valid, yet whether the ticket raised
an alert now or waited until the next morning depended on the model that happened
to answer. The lesson it draws is that valid JSON does not imply stable decisions.

The same caution applies to strict schema modes. The book's sidebar says a
constrained-decoding mode guarantees only the shape of a single completion. Tool
results injected between calls can shift how the model reads a field's value.
Enforcement applies at each generation boundary, not across a chain of agents, so
a handoff between two models is not checked as a whole. Some providers route
through proxies that may not implement constrained decoding, and fallback models
can assign different values to the same case. In the book's words, the best a
strict mode delivers is "well-formed JSON for a single completion call".

The practical test is therefore on the decision field rather than the format. Run
a fixed set of scenarios several times per model, compare the decision that
matters, and treat disagreement as a finding about the model or the fallback, not
as noise to average away. The book's tiering of enforcement is a useful frame: a
strict schema mode, client-side validation with retries, and prompt-only
canonicalisation for prototypes. It recommends at least the first two, and all
three for business-critical systems. Normalising the labels is covered in
[label normalization across models](../harness/label-normalization-across-models.md),
and the boundary that checks them is the [validation boundary gate](../orchestration/validation-boundary-gate.md).
When a fallback model is part of the design, test the whole pipeline with it, as
in [degradation ladder](../orchestration/degradation-ladder.md).
