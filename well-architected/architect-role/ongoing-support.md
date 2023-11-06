---
title: Support the workload in a consultative role
description: Learn about how an architect's continued consultative role supports the workload team after product implementation.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Support the workload in a consultative role

Architects should seek ways to stay involved with the workload as it changes over time. Their role doesn't end with design handoff or consultation during the initial implementation. Architects bring perspective that can be used in other activities that are related to the product's evolution.

## Support modeling exercises

Teams can model workloads in multiple dimensions and for multiple purposes. For example, workloads can abstract health signals out of implementation details and into business constructs. Or they can model system growth over time or the licensing process to evaluate alternative billing models.  

Whether the model is an abstraction or evaluates hypotheticals to inform a future business decision, architects contribute to that process. They use their insight into the workload design, its known or predicted limitations, and its scaling characteristics, to validate or tune the assumptions in the model and approximate the system more accurately. For example, architects review the health model for a critical flow by evaluating the characteristics of dependencies, such as service-level objectives (SLOs).

## Share potential improvements

Architects stay current with fundamentals like cloud provider offerings and industry design patterns. Features that were state of the art when a workload was designed might no longer be. Or the expected usage patterns of the application might not manifest in the way that they were predicted. In cases like these, there's an opportunity for you to present a recommendation to further optimize or refine the current design based on this new knowledge.

As an architect, you should follow up with the workload team periodically after the workload is live. Continued communication helps you expand your knowledge for future design work by seeing how the design was implemented and how it's performing with actual use. It also allows you to offer optimization recommendations based on the actual implementation and use.

## Assist in reviews

When a workload is under review, such as by an official audit or a compliance review, the system architect's involvement can be a boon to the process. They bring the workload's [architecture decision record](./architecture-decision-record.md) to help answer questions on implementation choices. They also provide updated diagrams to visualize the system during conversations and provide subject-matter expertise.

Architects have authoritative knowledge that builds confidence in the product during select customer or funding engagements. They can learn about unique demands that customers have for the product and consider those needs in the design of the system.

## Review proposed changes

Every workload has a backlog of work that ranges from broad, directional-level work to specific tasks. Architects should be involved in gathering requirements, scoping, and building acceptance criteria for work items.

The implementation team is busy delivering on current work items, so architects can use their time to review, validate, and refine future work items. They can help detect when a new feature requires a redesign of a component in the system, provide cost analysis on a proposed change, or propose an approach to incrementally introduce new changes. Ultimately, involving an architect early in the process for a proposed change that involves new functionality or an expanded user base minimizes rework and helps the team discover cliffs in the design.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)