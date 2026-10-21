---
type: concept
title: Stage-specific WIP accounting
description: >-
  Define WIP against explicit workflow start and finish points, and separate active build constraints from downstream acceptance queues.
evidence: weak
sources:
  - title: "The Kanban Guide (2025 edition)"
    resource: "Definition of Workflow, Work in Progress, Active Management — https://prokanban.org/the-kanban-guide"
  - title: "The Official Guide to The Kanban Method"
    resource: "Limit Work in Progress, Make Policies Explicit — https://kanban.university/kanban-guide/"
  - title: "Working with WIP limits for kanban"
    resource: "Software-team workflow example — https://www.atlassian.com/agile/kanban/wip-limits"
  - title: "GitHub Docs: Merging a pull request"
    resource: "About pull request merges — https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/merging-a-pull-request"
---

WIP accounting depends on the workflow boundary.[1]

The 2025 Kanban Guide defines WIP as items between a defined start and finish point, and requires an explicit workflow definition with states, start/finish points, and WIP control.[1]

That allows a work item to finish one stage while still flowing through a later stage; the stages and their completion criteria must be explicit.[1]

A repository build lock and a delivery/acceptance queue therefore answer different questions.[unverified]

Count active code-changing work against the build constraint; retain merged work that still awaits acceptance in a separate stage until its close criterion is met.[1][2]

Software-team guidance provides an adjacent example: active development and code review are distinct workflow states with separate WIP limits.[5]

That example is pre-merge review, not post-merge acceptance.[5]

GitHub's merge status reports the lifecycle of the pull request, not necessarily acceptance of the parent work item.[3][unverified]

GitHub distinguishes merging from closing without merge, and permits linking a PR to an issue that closes on merge.[3][4]

Using PR state as a signal for release of a build lock can therefore be useful.[unverified]

Treating it as the only truth risks missing residual implementation work; keeping merged cards in the total workflow until acceptance avoids hiding downstream WIP.[unverified]

This split is a design inference, not an agent-specific effectiveness finding.[unverified]

No retrieved source directly compares PR-state versus card-state locks or measures interaction between post-merge acceptance and build-stage limits.[unverified]

A separate acceptance cap and aging/escalation signal can make the bottleneck visible without making a passive acceptance wait occupy the code-change lock.[1][2][unverified]

## Sources

[1] https://prokanban.org/the-kanban-guide — The Kanban Guide (2025 edition)
[2] https://kanban.university/kanban-guide — The Official Guide to The Kanban Method
[3] https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/merging-a-pull-request — Merging a pull request - GitHub Docs
[4] https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/closing-a-pull-request — Closing a pull request - GitHub Docs
[5] https://www.atlassian.com/agile/kanban/wip-limits — Working with WIP limits for kanban - Atlassian
