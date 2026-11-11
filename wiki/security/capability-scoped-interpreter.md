---
type: concept
title: Capability-Scoped Interpreter
description: >
  Running model-generated code in an interpreter that starts empty and exposes only named host functions contains what the code can reach, where the same code run in an ordinary interpreter on the host can leak secrets.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, Sandboxing Agent Execution; Monty: Capability-Scoped Interpreter (Example 6-10); Isolation Layer Selection (Table 6-4)"
---

Code mode needs an isolation layer, and the book lists three. Containers give
strong operating-system process and filesystem control and suit headless or
internal tasks. Ephemeral cloud sandboxes give a fresh virtual machine per run,
suited to high-variance or untrusted code. A capability-scoped interpreter is the
third. It starts from an empty execution environment rather than a full host
environment with dangerous features removed. Filesystem, environment variables and
network are unavailable by default, and the program can call only the functions
the host exposes. The book names Monty, a minimal Python interpreter written in
Rust, as an example, with startup measured in microseconds against hundreds of
milliseconds for a container. It calls the project experimental.

The demonstration is the reason this matters. The same injected code, which reads
an environment variable, reads a file and sends data to an endpoint nobody
declared, is contained under the capability-scoped interpreter: unsupported
operating-system access, an undefined name and a permission error each stop it.
Run through plain `exec()` on the host, the same code leaks the secret. The
book's summary is that the only difference between containment and a breach is
the interpreter the model's code runs in. The demonstration is one run, which is
why the evidence is rated weak.

Two limits are stated. Because the interpreter runs inside the agent's process,
an escape is a compromise of the host, so it is defence in depth rather than the
outer wall, and genuinely untrusted code belongs inside a stronger tier. And
Docker, though useful for packaging, is not a hard security boundary. Hardware-
level microVMs and user-space syscall mediation are the stronger options the book
names. Host functions can also pause the program. The host checks the call, may
deny it by raising an exception inside the program so the model's code degrades
instead of escalating, or may run the call where its credentials live and resume
with the result. A paused run can be serialised and resumed later. The
[programmatic tool calling](../harness/programmatic-tool-calling.md) note covers
the execution pattern this interpreter serves, and [contain at the environment layer first](contain-environment-first.md)
gives the ordering of isolation controls.
