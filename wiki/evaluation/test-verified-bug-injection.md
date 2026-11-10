---
type: concept
title: Test-Verified Bug Injection
description: >
  Turning a repository into coding-agent evaluation cases by injecting bugs and using the repository's own tests as the verifier removes manual curation, provided each injected bug is confirmed to break a test.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 9, Turn Your Repo into a Benchmarkable Environment; Bug-Generation Strategies (Table 9-5)"
  - title: "SWE-smith"
    resource: "Cited in AI Agents (Koenigstein), ch. 9, SWE-smith workflow (Examples 9-14 to 9-18)"
---

The book's approach generates coding tasks directly from a codebase, instead of
curating them by hand as with SWE-bench. A task instance is a modified copy of the
repository that contains one bug, stored as a patch that breaks one or more of the
repository's existing tests. The repository is then three things at once: the source
of the cases, the environment they run in, and the verifier. Correctness is defined
by the repository's own tests. The agent passes if its fix makes them pass, so no
subjective score is needed.

Three strategies generate the bugs, and the book recommends combining them, since
each covers different entities in the code. A language model can rewrite a function
to introduce a subtle logical error, which is realistic but nondeterministic.
Procedural modifications on the syntax tree make structural changes that are
deterministic and reproducible, which gives systematic coverage. PR mirroring
reverts real pull requests to recreate bugs from the project's history, which is
realistic but limited by how many useful pull requests exist.

The workflow is to build an isolated image so that the copy cannot disturb the
environment, generate candidate bugs, and validate each one by checking that it
really breaks tests, keeping only validated instances. A proposed fix is then judged
by running the tests on the patched copy. Instances can be selected by criteria
such as how many tests fail, where the book uses two to five. Generation is cheap
compared with hand authoring, but candidates still need validation, and the book
does not claim generated bugs resemble real defects closely. Production traces show
the failures that actually happened, and generated bugs extend coverage beyond them.

Verifier-based evaluation shows whether a fix works, not how the agent reached it.
The trajectory of the run has to be read separately, as in
[coding agent eval design](coding-agent-eval-design.md) and
[root-cause step localisation](root-cause-step-localisation.md). Tests written by the
agent itself are a weaker verifier, as [test suite fidelity](test-suite-fidelity.md)
explains.
