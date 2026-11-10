---
type: concept
title: Hierarchical Supervision
description: >
  Supervisors of supervisors let a large agent system split into teams that each route their own work, which scales with the organisation but makes failures harder to trace.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 2, Advanced Agent Paradigms: Supervisor and Hierarchical Agents; Table 2-4"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 2, Hierarchical research and writing teams (Examples 2-22 to 2-30)"
---

A hierarchical multi-agent system places supervisors over supervisors, so that
a top-level supervisor routes between teams and each team has its own supervisor
routing among its workers. This allows more flexible control flow than a single
[supervisor](supervisor-routing.md), at the cost of more layers of decision.

The book's example builds a research team and a writing team, each as a
supervised graph (the research team has four specialised workers), and joins them in a
super graph. The top supervisor sends work to research, then to writing. The
research team's output is what the writing team consumes, and the writing team
saves its result to disk. A demonstration run produced an 800-word whitepaper
with patent links in about two minutes, and the supervisor's decision trace
(search, then patent research, then end) can be read back afterwards.

The book's comparison table lists the hierarchy as suited to larger multi-team
workflows, modular and scalable, and mirroring organisational structure. It
also lists the costs: harder to debug, and it needs careful design. The
interface between two teams is the main design point, because one team's output
becomes the next team's input. Define that shape explicitly, as in
[task I/O schema design](task-io-schema-design.md), so that a fault can be
localised to a team rather than to the whole tree. Trace the routing decision at
each level, since the path from a bad outcome to its cause crosses every
supervisor on the route. For peers passing control directly, see
[swarm handoff coordination](swarm-handoff-coordination.md).
