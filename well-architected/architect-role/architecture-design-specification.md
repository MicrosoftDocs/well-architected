---
title: Workload architecture design specification
description: Learn about the benefits of creating an architecture design specification for a workload. The specification describes design choices and is accompanied by diagrams.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Workload architecture design specification

A workload architecture design specification is **a detailed specification that describes design choices and is accompanied by diagrams**. The design choices must meet functional and nonfunctional requirements and include provisions for routine, ad-hoc, and emergency operations.

For information about diagrams, see [Architecture design diagrams](./design-diagrams.md).

Workload architecture design, typically expansive, starts with application design and progresses to cloud service selection. These phases mutually inform each other. The combined application and infrastructure design must fulfill all requirements.

Achieving a solution that satisfies all requirements is a **collaborative effort among stakeholders, developers, testers, operations teams, and product owners**. The design process should involve refining requirements with clarity and negotiation. The process is iterative and often requires multiple reviews.

We recommend that you align your design with the Azure Well-Architected Framework fundamental guidance, which includes design principles and recommendation guides, and acknowledge the tradeoffs.

Ultimately, the workload architecture design specification is implemented by the workload development team, so it must be clear and unambiguous. The specification should be readily available and stored with the workload's documentation.

## Disaster recovery plans

In order to meet the reliability requirements for the workload, an architect needs to design a system that can recover within the target recovery time objective (RTO) and recovery point objective (RPO) goals. The architecture design specification must include the recovery plan. This plan must cover the involved architecture components, failover mechanisms and impact to user and data flow, and operational recommendations. It should describe which recovery targets are met by the design and how.

Although the initial plan is expected to evolve based on insights from drills and post-incident reviews, it's the architect's responsibility to deliver the initial plan for all new architecture.

## Security and compliance documentation

An architect is responsible for designing a solution that adheres to pertinent security and compliance constraints. It's important for the design artifacts to highlight the affordances incorporated in the design to support these requirements, and to identify any necessary compensating controls when requirements can't be met directly.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
