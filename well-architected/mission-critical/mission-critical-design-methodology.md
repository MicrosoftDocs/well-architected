---
title: Design methodology for mission-critical workloads
description: A design methodology to guide readers through the architectural process of building a mature mission-critical application on Microsoft Azure.
author: calcof
ms.author: calcof
ms.date: 02/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - management-and-governance
  - web
products: Azure
ms.custom:
  - mission-critical
---

# Design methodology for mission-critical workloads

Building a mission-critical application on any cloud platform requires significant technical expertise and engineering investment. Many factors can make the design process complex,
- Understanding the cloud platform, 
- Choosing the right services, 
- Configuring the right set of features, 
- Constantly aligning with the latest best practices, and 
- All while meeting the business requirements. 

This design methodology aims to provide an easy design path to follow guidance about the critical architecture decisions needed to produce the target architecture.

<!insert visual>

## 1&mdash;Design for business requirements

Not all mission-critical applications have the same requirements. It's expected that the review considerations and design recommendations will vary according to the business requirements. So every implementation will be based on different design decisions and trade-offs.

### Define the reliability tier

Reliability of an application will change according to the business requirements. An initial exercise is to determine how much downtime is acceptable? Instead of defining availability as simple uptime, consider a consistent level of application service relative to a known healthy application state captured by a codified health model.

Reliability tier is expressed as availability Service Level Objectives (SLOs). This image shows example reliability tiers for a year and how they can vary for a mission critical application.  

![AlwaysOn Reliability Tiers](./images/alwayson-reliability-tiers.png "AlwaysOn Reliability Tiers")

To meet a 99.999% availability Service Level Objective (SLO), a mission-critical application will require much higher level of reliability and operational rigor than another application with an SLO of 99.9%. The pursuit of a particular reliability tier ultimately has a significant bearing on the critical design path and encompassed design decisions. Each path will result in a different target architecture.

> [!IMPORTANT]
> This example shows the expected annual downtime. We recommend defining reliability tiers for a week and month to introduce further specificity to design recommendations.

Here's the reliability tier for the AlwaysOn misson-critical application. It shows how the different reliability tiers and the underlying business requirements influence the target architecture. Particularly the number of regional deployments and the used global technologies.

![AlwaysOn Reliability Tiers](./images/alwayson-slo.gif "AlwaysOn Reliability Tiers")

### Consider the trade-off on cost
There are obvious financial and opportunity cost implications for introducing greater reliability. Consider the trade-offs carefully.

There is cost associated with the significant engineering investment in reliability concepts, such as fully embracing infrastructure as code, deployment automation, and chaos engineering. This investment comes at a cost in terms of time and effort, which could be invested elsewhere to deliver new application functionality and features.

Furthermore, maximizing reliability can impact overall cost of the solution. For example, to achieve high availability you might choose to globally distribute the application. This will lead to duplication of resources and the distribution of resources across regions. To avoid excess costs, optimize by avoiding over-engineering and over-provisioned beyond the relevant business requirements.

## 2&mdash;Refer to the AlwaysOn implementations
The AlwaysOn project strives to illustrate the design decisions.

> ![GitHub logo](./../_images/github.svg) [AlwaysOn open source project](http://github.com/azure/alwayson)

- **Production-ready artifacts**: Every technical artifacts are ready for use in production environments with all end-to-end operational aspects considered. There are two [reference implementations](mission-critical-overview.md#illustrative-examples):

  - [Azure AlwaysOn Foundational Online](https://github.com/azure/alwayson-foundational-online)

  - [Azure AlwaysOn Foundational Connected](https://github.com/azure/alwayson-foundational-connected) 

  Each set contains guidance that provides prescriptive and opinionated technical guidance alongside streamlined consumption mechanisms for common industry patterns.

- **Rooted in 'customer truth'** - All technical decisions are guided by the real-wold experiences of Azure customers and lessons learned from those engagements.

- **Azure roadmap alignment** - The AlwaysOn architecture has its own roadmap that is aligned with Azure product roadmaps.

## 3&mdash;Evaluate the design areas using the design principles

At the core a critical design path, comprised of  foundational design principles and fundamental design areas with heavily interrelated and dependent design decisions.

Apply the [design principles](mission-critical-design-principles.md) of quality pillars of the Azure Well-Architected Framework&mdash;[Reliability](/azure/architecture/framework/#reliability), [Peformance Efficiency](/azure/architecture/framework/scalability/), [Operational Excellence](/azure/architecture/framework/devops/), [Security](/azure/architecture/framework/security/). In addition strive for a cloud native design. 

The impact of decisions made within each [design area](mission-critical-design-areas.md) will reverberate across other design areas and design decisions. Review the provided considerations and recommendations to better understand the consequences of encompassed decisions, which may later produce trade-offs or unforeseen consequences within related areas. For example, to define a target architecture it is critical to determine how best to monitor application health across key components. In this instance, the reader should review the health modeling design area, using the outlined recommendations to help drive decisions.

## 4&mdash;Deploy environment for a synthetic application

In parallel to design activities, it's highly recommended that you establish a synthetic  application environment based on the AlwaysOn reference implementations.

This provides hands-on opportunities to validate design decisions by replicating the target architecture, allowing for design uncertainty to be swiftly assessed. If applied correctly with representative requirement coverage, most problematic issues likely to hinder progress can be uncovered and subsequently addressed.

## Target architecture evolution

Application architectures established using this design methodology must continue to [evolve in alignment with Azure platform roadmaps](/azure/architecture/guide/design-principles/design-for-evolution) to support optimized sustainability.

## Next step

Review  the design principles for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Design principles](mission-critical-design-principles.md)
