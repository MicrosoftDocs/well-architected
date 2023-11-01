---
title: Workload architecture design specification
description: Learn about the benefits of an architecture design specification.
author: ckittel
ms.author: chkittel
ms.date: 10/26/2023
ms.topic: conceptual
---

# Workload architecture design specification

Workload architecture design is **a detailed specification that describes design choices accompanied by diagrams**. The design choices must meet functional and nonfunctional requirements, and includes provisions for routine, ad-hoc, and emergency operations.

For information about diagrams, see [Architecture design diagrams](./design-diagrams.md).

Workload architecture design, typically expansive, starts with application design and progresses to cloud service selection, mutually informing each other. The combined application and infrastructure design must fulfill all requirements.

Achieving a solution that satisfies all requirements  is a **collaborative effort among stakeholders, developers, testers, operations teams, and product owners**. The design process should involve refining requirements with clarity and negotiation. The process is iterative and often requires multiple reviews.

Microsoft recommends that the design should align with the Azure Well-Architected Framework (WAF) fundamental guidance that includes design principles, recommendation guides, and acknowledge the tradeoffs.

Ultimately, the workload architecture design specification is implemented by the workload development team, so it must be clear and unambiguous. These artifacts should be readily available and stored with the workload’s documentation.

## Disaster recovery plans

To meet the reliability requirements for the workload, an architect is responsible for designing a system that can recover within the target Recovery time objective (RTO) and Recovery point objective (RPO)  window. The architecture design specification must include the recovery plan. This plan must cover the involved architecture components, failover mechanisms and impact to user and data flow, and operational suggestions. It should be able to show which recovery targets are met by the design and how.

While this initial plan is anticipated to evolve based on insights from drills and post-incident reviews, it's the architect's responsibility to deliver the initial plan for all new architecture.

## Security and compliance documentation

An architect is responsible for designing a solution that adheres to pertinent security and compliance constraints. It's important for the design artifacts to highlight the affordances incorporated in the design to support these requirements, and to identify any necessary compensating controls when requirements cannot be directly fulfilled.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
