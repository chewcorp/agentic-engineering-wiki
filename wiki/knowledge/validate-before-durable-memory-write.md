---
type: concept
title: Validate Before Durable Memory Write
description: >
  Checking each candidate memory for length and for sensitive patterns before it is persisted keeps secrets out of long-term storage and stops one wrong or planted fact from becoming trusted context later.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 10, Memory Hygiene: Validation Before Durable Writes"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 10, Memory Failure Propagation"
---

The book's starting point is that memory is not neutral. It can amplify bias and
error, and incorrect information stored as truth compounds through later
reasoning. For that reason it makes validation before persistence mandatory. Its
checks are simple. Content over a maximum length is rejected. Content matching
sensitive patterns is rejected, and the book lists social-security-style numbers,
payment-card-like digit runs of 13 to 19 digits, PEM private-key blocks, AWS access
key identifiers and common API secret prefixes. Only content that passes goes on to
the update step.

Regex checks are cheap, deterministic and predictable. They catch what has a
recognisable shape and miss a secret that is paraphrased or split across sentences,
so they are a floor rather than a guarantee. The same caution holds for the content
checks: a regex cannot tell whether a fact is true.

Source trust is the other half. A claim that a user makes about a past event can be
planted deliberately, and [memory hacking](../security/memory-hacking.md) describes
how false past events, once archived, return through retrieval and reflection as if
they were real. The defence the wiki records there is to distrust unverified claims
before an archival write and to tag provenance. Validation is the place to apply
that rule, because after the write the record looks the same as any other.

The checks should be ordered by cost. A rule-based check runs on every candidate,
and an LLM judge is reserved for the cases that remain ambiguous, which is the
tiering in [memory write decision cost tiers](memory-write-decision-cost-tiers.md).
Accepted records then go through [structured memory updates](structured-memory-updates.md),
and the namespace they are written into is set by [memory namespace scoping](memory-namespace-scoping.md).
