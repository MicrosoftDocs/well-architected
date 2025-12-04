---
title: Develop an architecture design specification
description: Learn about the benefits of creating an architecture design specification for a workload. The specification describes design choices through words and diagrams.
author: ckittel
ms.author: chkittel
ms.date: 07/25/2024
ms.topic: concept-article
ms.update-cycle: 1095-days
---

# Develop an architecture design specification

A workload architecture design specification is **a detailed specification that describes design choices supported by architecture diagrams and justifications**. These decisions must address both functional and nonfunctional choices, as well as support routine, ad hoc, and emergency operations. All of this, however, must be rooted in clear business needs. If you haven't yet established a well-defined set of business goals agreed upon with stakeholders, we recommend starting with the guide on [Align technical strategy with business requirements](./design-business-requirements.md).

Workload architecture design, typically expansive, starts with application design and progresses to cloud service selection. These phases mutually inform each other. The combined application and infrastructure design must fulfill all requirements.

Achieving a solution that satisfies all requirements is a **collaborative effort among stakeholders, developers, testers, operations teams, and product owners**. The design process should involve refining requirements with clarity and negotiation. The process is iterative and often requires multiple reviews.

We recommend that you align your design with the Azure Well-Architected Framework fundamental guidance, which includes design principles and recommendation guides, and acknowledge the tradeoffs.

Ultimately, the workload architecture design specification is implemented by the workload development team, so it must be clear and unambiguous. The specification should be readily available and stored with the workload's documentation.


## Technical specification

The technical specification describes the *how* based on the scope and goals described in the [functional specification](./design-business-requirements.md#expected-outcome). This specification is designed for the engineering team to use as a plan-of-record during implementation.

In this specification include items such as:

- Technology decisions, including as: buy, build, reuse, extend, or decommission.
- API and data contracts (schemas), including backwards compatibility implementation strategy
- Rollout and rollback implementation details
- Unique secure development lifecycle (SDL) and privacy implementation
- The test plan
- Key monitoring and alert signal sources
- Alternative designs that were considered

The technical specification will drive engineering efforts. Engineering work items are primarily created from the contents this specification. Implementation teams refer to the work items, the technical specification, and the functional specification to ensure the final result is meeting both the functional and nonfunctional requirements.

## Disaster recovery (DR) plans

In order to meet the reliability requirements for the workload, an architect needs to design a system that can recover within the target recovery time objective (RTO) and recovery point objective (RPO) goals. The architecture design specification must include the recovery plan. This plan must cover the involved architecture components, failover mechanisms and impact to user and data flow, and operational recommendations. It should describe which recovery targets are met by the design and how.

Although the initial plan is expected to evolve based on insights from drills and post-incident reviews, it's the architect's responsibility to deliver the initial plan for all new architecture.

For details on how to build a DR plan, see [Develop a disaster recovery plan for multi-region deployments](../design-guides/disaster-recovery.md).

## Security and compliance documentation

An architect is responsible for designing a solution that adheres to pertinent security and compliance constraints. It's important for the design artifacts to highlight the affordances incorporated in the design to support these requirements, and to identify any necessary compensating controls when requirements can't be met directly.

## Consistency

Use a template to document your workload's various specifications. Consistency helps stakeholders, responsible parties, and implementation teams when the specification is being read.

Ensure specs have key metadata fields such as:

- **State**: A status as *Draft*, *In review*, and *Approved*.
- **Work item link**: A link to the primary work item in the team's backlog.
- **Key cross links**: Links to related specifications or other documentation that supports the specification.
- **Key individuals**: A place to list the names of key decision makers involved. This list might include roles like: business analyst, business partner, technical lead, and the product owner or lead that signed off on the specification.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
