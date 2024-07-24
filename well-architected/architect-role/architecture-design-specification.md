---
title: Workload architecture design specification
description: Learn about the benefits of creating an architecture design specification for a workload. The specification describes design choices through words and diagrams.
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

The functional specification for a workload details the *what* and the *why* of the system or feature under development, but not the implementation. This document must explain the current problems that exist and how this feature or system is going to improve that experience. This document captures most of the business requirements.

An architect can help shape this document, but primarily it's a function of product ownership. An architect should help design the data that is captured in this specification. This involvement ensures the functional specification drives effective and efficient technical design.

Here are a few example topics that should be covered in this specification.

- In addition to detailing what is *in scope* for this design, also be explicit about adjacent concerns that are *out of scope*. Setting clear scopes reduces scope creep by defining boundaries around the functionality.

- It's helpful to include the details on how this change is going to be measured. What measurements need to be collected and what business goals those measurements support.

- User flows should be clearly described. Low-fedelity mockups can be helpful as well. If error handling situations are important for these flows, ensure the expected behavior described.

- Always include any specific requirements for accessibility, compliance, performance, privacy, or security.

- Include any planned rollout strategy. For example, "This feature is going to be available for our beta users for two months before deciding on a full release."

Avoid specific technical implementation details in this specification. These functional specifications will drive technical specifications created by the architect.

## Technical specification

The technical specification describes the *how* based on the scope and goals described in the [functional specification](#functional-specification). This specification is designed for the engineering team to use as a plan-of-record during implementation.

In this specification include items such as:

- Technology decisions, including as: buy, build, reuse, extend, or decommission.
- API and data contracts (schemas), including backwards compatibility implementation strategy
- Rollout and rollback implementation details
- Unique secure development lifecycle (SDL) and privacy implementation
- The test plan
- Key monitoring and alert signal sources
- Alternative designs that were considered

The technical specification will drive engineering efforts. Engineering work items are primarily created from the contents this specification. Implementation teams refer to the work items, the technical specification, and the functional specification to ensure the final result is meeting both the functional and nonfunctional requirements.

## Disaster recovery plans

In order to meet the reliability requirements for the workload, an architect needs to design a system that can recover within the target recovery time objective (RTO) and recovery point objective (RPO) goals. The architecture design specification must include the recovery plan. This plan must cover the involved architecture components, failover mechanisms and impact to user and data flow, and operational recommendations. It should describe which recovery targets are met by the design and how.

Although the initial plan is expected to evolve based on insights from drills and post-incident reviews, it's the architect's responsibility to deliver the initial plan for all new architecture.

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
