---
title: Workload architecture design specification
description: Learn about the benefits of creating an architecture design specification for a workload. The specification describes design choices and is accompanied by diagrams.
author: ckittel
ms.author: chkittel
ms.date: 07/25/2024
ms.topic: conceptual
---

# Workload architecture design specification

A workload architecture design specification is **a detailed specification that describes design choices and is accompanied by diagrams**. The design choices must meet functional and nonfunctional requirements and include provisions for routine, ad hoc, and emergency operations.

For information about diagrams, see [Architecture design diagrams](./design-diagrams.md).

Workload architecture design, typically expansive, starts with application design and progresses to cloud service selection. These phases mutually inform each other. The combined application and infrastructure design must fulfill all requirements.

Achieving a solution that satisfies all requirements is a **collaborative effort among stakeholders, developers, testers, operations teams, and product owners**. The design process should involve refining requirements with clarity and negotiation. The process is iterative and often requires multiple reviews.

We recommend that you align your design with the Azure Well-Architected Framework fundamental guidance, which includes design principles and recommendation guides, and acknowledge the tradeoffs.

Ultimately, the workload architecture design specification is implemented by the workload development team, so it must be clear and unambiguous. The specification should be readily available and stored with the workload's documentation.

## Functional specification

The functional sepcification for a workload details the *what* and the *why* of the system or feature under development, but not the implementation. This document must explain the current problems that exist and how this feature or system is going to improve that experience. These make up the bulk of business requirements.

An architect can help shape this document, but primarily it's a function of product ownership. An architect should help design the information that is captured in this specification to enable a comprehensive technical specification to be generated from this.

Here are a few example topics that should be covered in this specification.

- In addition to detailing what is *in scope* for this design, also detail adjacent concerns that have been decided to be *out of scope*. This helps reduce scope creep by defining boundries around the functionality.

- It's helpful to include the details on how this change is going to be measured. What measurements are expected to be collected and what goals those measurements support.

- User flows should be clearly described. Low-fedelity mockups can be helpful as well. If error handling situations are important for these flows, ensure the expected behvior described.

- Always include any specific requirements for accessibility, compliance, performance, privacy, or security.

- Include any planned rollout strategy. For example, "This is going to be available for our beta users for two months before deciding on a full release."

Avoid specific technical implementation details in this specification. These functional sepcifications will help drive technical specifications created by the architect.

## Technical specification

The technical specification describes the *how* based on the scope and goals set forth in the [functional specification](#functional-specification). This specification is designed for the engineering team to use as a plan-of-record during implementation.

In this specification include items such as:

- Technology decisions, including as: buy, build, reuse, extend, or decomission.
- API and data contracts (schemas), including backwards compatibility implementation strategy
- Rollout and rollback implementation details
- Unique secure development lifecycle (SDL) and privacy implementation
- The test plan
- Key monitoring and alert signal sources
- Alternative designs that were considered

The technical specification will be used to drive engineering efforts through work items. Those engineering work items are primarly created from the contents this specification. Implementation teams will refer to the work items, the technical specification and the functional specification to ensure the final result is meeting both the functional and non-functional requirements.

## Disaster recovery plans

In order to meet the reliability requirements for the workload, an architect needs to design a system that can recover within the target recovery time objective (RTO) and recovery point objective (RPO) goals. The architecture design specification must include the recovery plan. This plan must cover the involved architecture components, failover mechanisms and impact to user and data flow, and operational recommendations. It should describe which recovery targets are met by the design and how.

Although the initial plan is expected to evolve based on insights from drills and post-incident reviews, it's the architect's responsibility to deliver the initial plan for all new architecture.

## Security and compliance documentation

An architect is responsible for designing a solution that adheres to pertinent security and compliance constraints. It's important for the design artifacts to highlight the affordances incorporated in the design to support these requirements, and to identify any necessary compensating controls when requirements can't be met directly.

## Consistency

Create and wse a template to document your workload's various specifications. Consistency helps stakeholders and responsible parties when reviewing and approving specifications.

Ensure specs have key metadata fields such as:

- **State**: A status as Draft, In review, and Approved so individuals not familar with the current state of development knows which drafts are still open for negotiation and influence and which have already been signed off on.
- **Work item link**: A link to the primary work item in the team's backlog.
- **Key individuals**: A place to list the names of people involved in this, such as the business analyist, a business partner, the technical lead, and the product owner or lead that signed off on the specification.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
