---
title: Design methodology for carrier-grade workloads on Azure
description: Understand the architectural process of building a carrier-grade application on Microsoft Azure.
author: mikedell73
ms.author: mikedell
ms.date: 07/05/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.industry: telecommunications
products: 
- azure-app-service
- azure-traffic-manager
- cosmos-db
ms.custom:
  - carrier-grade
---

# Design methodology for carrier-grade workloads on Azure

Building a carrier-grade application on any cloud platform requires significant technical expertise and engineering investment, particularly since there's significant complexity. However, designing well-architected applications for carrier grade will actively aim to reduce complexity (subject to meeting feature requirements), particularly for the following fundamentals:

- [Fault tolerance](carrier-grade-design-area-fault-tolerance.md)
- [Considered data model](carrier-grade-design-area-data-model.md)
- [Built-in health modeling for management and monitoring](carrier-grade-design-area-health-modeling.md)
- [Extensive testing and validation](carrier-grade-design-area-testing.md)

## 1&mdash;Design for business requirements

When the architect considers carrier-grade reliability, they must start from the assumption that everything can, and will fail. Application design must allow for these failures with fault tolerance so that an application can continue to operate at some level.

Carrier-grade design must satisfy the following business requirements:

- Architects must minimize single points of failure and implement a [federated approach](carrier-grade-design-area-health-modeling.md#federated-model).
- Carrier-grade availability inherently requires that you deploy the application across multiple regions, which requires careful consideration about how the application's data will be managed across those regions. For more information, reference the [CAP theorem](carrier-grade-design-area-data-model.md#cap-theorem).
- High availability requires careful [health monitoring](carrier-grade-design-area-health-modeling.md) so that issues can be detected automatically and responded to within seconds.
- Design and implementation of the application, plus integration and deployment as a full solution must be tested to find and resolve issues. This testing should include [chaos testing](carrier-grade-design-area-testing.md) on production systems to avoid testing bias.
- Highly available systems rely on support teams to:
  - Respond and resolve issues in the field
  - Conduct root cause analysis
  - Find systematic design flaws

## 2&mdash;Refer to the carrier-grade implementation

Carrier-grade workloads strive to illustrate the design recommendations provided by this methodology, and should be used as a source of reference to inform the overall design strategy.

Refer to the foundational [Mobile Voice Mail (MVM) reference implementation](carrier-grade-get-started.md#reference-implementation-mobile-voice-mail-mvm).

## 3&mdash;Evaluate design areas using design principles

At the core of this methodology, lies a critical design path comprised of foundational [design principles](carrier-grade-design-principles.md) and fundamental [design areas](carrier-grade-architecture-pattern.md) with heavily interrelated and dependent design decisions.

Decisions made within each design area will reverberate across other design areas and design decisions. Review the provided considerations and recommendations to better understand the consequences of encompassed decisions, which may produce trade-offs within related design areas.

For example, to define a target architecture it's critical to determine how best to monitor application health across key components. We highly recommend that you review the [health modeling design area](carrier-grade-design-area-health-modeling.md), using the outlined recommendations to help drive decisions.

## 4&mdash;Deploy a sandbox application environment

Alongside design activities, it's highly recommended that you establish a sandbox application environment using the [carrier-grade reference implementation](carrier-grade-get-started.md#reference-implementation-mobile-voice-mail-mvm).

An application environment provides hands-on opportunities to validate design decisions by replicating the target architecture, allowing your team to assess design uncertainty. If applied correctly with representative requirement coverage, most problematic issues that impede progress can be uncovered and addressed.

## Target architecture evolution

Application architectures established using this design methodology must continue to [evolve in alignment with Azure platform roadmaps](/azure/architecture/guide/design-principles/design-for-evolution) to support optimized sustainability.

## Next step

Review the design principles for carrier-grade application scenarios.

> [!div class="nextstepaction"]
> [Design principles](carrier-grade-design-principles.md)

