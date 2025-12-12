---
title: Support the workload in a consultative role
description: Learn about how an architect's continued consultative role supports the workload team after product implementation.
author: ckittel
ms.author: chkittel
ms.date: 11/24/2025
ms.topic: concept-article
ms.update-cycle: 1095-days
---

# Support the workload in a consultative role

Architects should seek ways to stay involved with the workload as it changes over time. Their role doesn't end with design handoff or consultation during the initial implementation. Architects bring perspective that can be used in other activities related to the product's evolution.

## Support modeling exercises

Teams can model workloads in multiple dimensions and for multiple purposes. These models often build on pre-deployment assumptions. For example, workloads can abstract health signals out of implementation details and into business constructs. Or they can model system growth over time or the licensing process to evaluate alternative billing models.

Whether the model is an abstraction or evaluates hypotheticals to inform a future business decision, architects contribute to that process. They use their insight into the workload design, its known or predicted limitations, and its scaling characteristics, to validate or tune the assumptions in the model and approximate the system more accurately. For example, does the health model accurately predict failures and align with the workload's service-level objectives (SLOs)? Is the cost model tracking with actual spend? You ensure that business decisions are based on empirical evidence rather than outdated hypotheses.

## Share potential improvements

Architects stay current with fundamentals like cloud provider offerings and industry design patterns.

- Features that were state of the art when a workload was designed might now be missing out on new cloud capabilities that offer better performance, security, or cost efficiency.
- The expected usage patterns of the application might not manifest as predicted.
- The workload might have accumulated "technical debt" or drifted from its original design principles due to rapid iteration.

In cases like these, there's an opportunity for you to present a recommendation to further optimize or refine the current design based on this new knowledge.

Don't wait for a crisis to suggest improvements. Proactively evaluate the workload against the current technology landscape and the evolving business strategy. Identify opportunities to simplify complexity or pay down technical debt.

As an architect, you should follow up with the workload team periodically after the workload is live. Continued communication helps you expand your knowledge for future design work by seeing how the design was implemented and how it's performing with actual use. It also allows you to offer optimization recommendations based on the actual implementation and use.

## Assist in reviews

Audits, compliance reviews, and due diligence events can be stressful for workload teams. Architects reduce the burden by providing the authoritative context.

Leverage the [Architecture Decision Record (ADR)](./architecture-decision-record.md) and [diagrams](./design-diagrams.md) to demonstrate that design choices were made deliberately and aligned with requirements at the time. Use these engagements not to defend the current state, but to identify gaps where the compliance landscape has shifted.

Architects have authoritative knowledge that builds confidence in the product during select customer or funding engagements. They can learn about unique demands that customers have for the product and consider those needs in the design of the system.

## Review proposed changes

Every workload has a backlog of work that ranges from broad, directional-level work to specific tasks. Architects should be involved in gathering requirements, scoping, and building acceptance criteria for work items.

The implementation team is busy delivering on current work items, so architects can use their time to review, validate, and refine future work items. They can help detect when a new feature requires a redesign of a component in the system, provide cost analysis on a proposed change, or propose an approach to incrementally introduce new changes. Ultimately, involving an architect early in the process for a proposed change that involves new functionality or an expanded user base minimizes rework and helps the team discover cliffs in the design.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
