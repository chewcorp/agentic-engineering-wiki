---
type: concept
title: Label Normalization Across Models
description: >
  Mapping each model's free-form labels onto a fixed vocabulary, and rejecting unmappable values where they drive escalation, keeps a system from changing behaviour silently when the model changes.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 7, Model Drift and Normalization Maps (Example 7-5); Empirical Drift Results"
---

Different models express the same meaning with different labels. One model
writes "critical", another "urgent", a third "p0". A schema that accepts any
string passes all three, and the downstream rules quietly behave differently for
each. The fix is a fixed vocabulary, enforced locally with normalisation maps
that translate synonyms into canonical values.

The book's maps cover three fields. Categories map related phrases onto one
label, so that "authentication" and "account access" both become "login".
Priorities map "critical" to the top level, "urgent" and "high" to the next, and
so on down. Sentiment maps "anxious" and "urgent" onto "frustrated". The book
stresses that priority needs the most care, because a single flip changes
escalation and whether someone is paged.

Each field needs its own failure rule. A category that cannot be mapped can fall
back to a neutral "other". Sentiment can default conservatively to the stronger
negative label. Priority should raise an error when unmappable, because a silent
default would decide whether a person is woken up. The provider's constrained
output keeps the shape, and the local validator keeps the values, which is why
both layers are used.

The book's empirical point is that four models triaged the same ticket with
valid JSON in every case, yet the outcome changed between models and between
runs. Normalising the labels makes that change visible in logs, where a fallback
was used, instead of hiding it. See [schema-valid decisions can diverge](../evaluation/schema-valid-decisions-can-diverge.md)
for the evaluation side, and the [validation boundary gate](../orchestration/validation-boundary-gate.md)
for where the mapping runs.
