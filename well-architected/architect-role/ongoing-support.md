---
title: Support the workload in a consultative role
description: Learn about being in a consultative role with the workload team after implementation.
author: ckittel
ms.author: chkittel
ms.date: 10/26/2023
ms.topic: conceptual
---

# Support the workload in a consultative role

An architect should seek ways to stay involved with the workload as it undergoes changes over time. The role of an architect doesn't end with design handoff or even consultation during initial implementation. The perspective an architect brings to a workload can be used in other activities related to the evolution of the product.

## Support modeling exercises

A workload can be modeled in multiple dimensions and for multiple purposes. For example, abstracting health signals out of implementation details into business constructs, modeling system growth over time, or modeling the licensing process to evaluate alternative billing models for the workload.  Whether the model is an abstraction or is to evaluate hypotheticals to inform a future business decision, an architect can contribute to that process. The insight in the workload design, it's known or predicted limitations, and it's scaling characteristics can be used to validate or tune the assumptions in the model to better approximate the system. For example, an architect can review the health model for a critical flow by evaluating the assumptions made about the characteristics of dependencies, such as their SLOs.

## Share potential improvements

An architect stays current with fundamentals such as cloud provider offerings and industry design patterns. What might have been state of the art when a workload was designed may no longer be. Or the expected usage patterns of the application didn't manifest itself in the way they were predicted. In cases like these, there may be an opportunity for an architect to present a design change recommendation to further optimize or refine the current design based on this new knowledge.

A general practice is to follow up with the workload team periodically after the workload is live. This allows the architect to learn for themselves about how the design was implemented and performing under actual usage to further improve their own knowledge for future design work. But also allows the architect to offer optimization recommendations based on the actual implementation and usage.

## Assist in reviews

When a workload is under review such as by an official audit or compliance, having the system architect involved can be a boon to the process. Architects can bring the workload's [architecture decision record](./architecture-decision-record.md) to help answer questions on implementation choices, provide updated diagrams to visualize the system during conversations, and provide subject-matter expertise. Likewise, the authoritative knowledge that an architect possess can be used during select customer or funding engagements to build confidence in the product by a potentially interested party, and can be available to learn about any unique demands that party might have for the product that might need to be considered in the design of the system.

## Review proposed changes

Every workload has a backlog of workload ranging from broad epic, directional level work, to specific tasks that need to be accomplished. As an architect, find ways to be involved in the requirements gathering, scoping, and acceptance criteria building for work items. As the implementation team is usually busy with delivering on current work items, an architect can provide time to review, validate, and refine work future work items. They can help detect when a new feature may require a redesign of a component in the system, provide cost analysis on the proposed change, or propose a approach to introduce the new changes incrementally. Ultimately, bringing an architect in early in proposed changes involving new functionality or an expanded user base can minimize rework or prevent being surprised by unknown cliffs in the design.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
