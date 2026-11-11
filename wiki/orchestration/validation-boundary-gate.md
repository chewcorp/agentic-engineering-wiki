---
type: concept
title: Validation Boundary Gate
description: >
  Validating each task's output at the boundary where it enters the next step stops a malformed value from spreading through a multi-agent system, and gives the operator a point to resume from.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 5, Contracts and Validation: Pydantic as a boundary gate; Brittle JSON trap"
---

In a multi-agent system, a model's output becomes the next agent's input, so an
error that is accepted at one boundary becomes global state downstream. The
book's failure cases are ordinary: a string where an integer is expected, a
missing field, a slightly different shape. Such mismatches pass staging tests
with clean inputs and break in production. The book's example is a processing
function that fails on a user identifier sent as the string "123". The fix is a
typed model at the boundary (an integer identifier, an age constrained to a
range) that either rejects the value or normalises it.

Validating at the boundary is what makes the rest of the system controllable.
A failed gate can change a setting, reroute, or resume from the last stable
checkpoint, without rerunning every upstream agent. That saves both cost and
latency. The gate should also emit an explicit success or failure signal, so
that routing never depends on an implicit assumption, and it should increment
the retry count centrally, so that retries stay bounded and observable.

The book also argues that validation should return feedback the model can act
on. A raw traceback or stack trace is less useful than one clear sentence
naming what was wrong, and [failures returned as actionable feedback](../harness/failures-returned-as-actionable-feedback.md)
gives the wider principle. Where the gate sits matters for retries, which
[retry at the failure source](retry-at-failure-source.md) covers. The contract
it checks should be fixed for the duration of a run, as in
[pinned validation contract](pinned-validation-contract.md). For the reliability
arithmetic that makes a gate worth its cost, see
[compound mistake amplification](compound-mistake-amplification.md). Defining the
shape of each task's input and output is the prerequisite, covered in
[task I/O schema design](task-io-schema-design.md).
