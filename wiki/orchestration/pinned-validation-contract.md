---
type: concept
title: Pinned Validation Contract
description: >
  Loading the validation schema and its governance policy once at the start of a run, and fingerprinting them, stops rules from drifting mid-execution and lets each stored record show which rules applied.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 5, Policy-Driven Contracts; Pinning and Hashing Against Mid-Run Drift"
---

A run that validates against a schema loaded from live configuration can change
its rules between steps: a policy edit lands while the graph is running, and the
later steps are checked against different rules from the earlier ones. The book
prevents this by pinning the contract at graph start. A node loads the active
policy, computes a policy hash for caching and audit, pre-builds the validation
models for that policy, and computes a schema hash, a truncated SHA-256 of the
normalised rules, that acts as a fingerprint. Validation nodes then use the
pinned contract for the rest of the run.

Rules that a JSON Schema cannot express, such as required keywords, forbidden
markdown or a required style tag, must be extracted explicitly and normalised
before hashing. Otherwise the fingerprint would miss the constraints that
actually reject output.

Policies can switch modes without a code deployment. A strict mode fails hard,
and a lenient mode returns controlled feedback. The model classes are built from
the active policy at runtime with a versioned title, so the contract can be
tailored to the model and rules in force. The book's point about models is that
different models fail in different ways, so one fixed contract can be wrong for
some of them.

Each stored record should carry the run identifier, the policy hash and the
schema hash. That ties a row to its checkpoint thread and shows which rules were
in play when it was written, which is the auditability pillar in the book. The
same design gives fault tolerance, because a failed node can rerun against the
same contract, and it argues for decoupling the external tool-result contract
from the internal persistence contract so that an upstream API change does not
break stored data. Pinning covers one run. It does not stop a later policy change
from altering how new runs validate, and nothing in the source addresses that.
Related: [validation boundary gate](validation-boundary-gate.md) and
[structured run state](structured-run-state.md).
