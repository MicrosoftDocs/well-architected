---
title: Design methodology for mission-critical workloads on Azure
description: Understand the architectural process of building a mature mission-critical application on Microsoft Azure.
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

# Design methodology for mission-critical workloads on Azure

Building a mission-critical application on any cloud platform requires significant technical expertise and engineering investment, particularly since there's significant complexity associated with:

- Understanding the cloud platform,
- Choosing the right services and composition,
- Applying the correct service configuration, 
- Operationalizing utilized services, and
- Constantly aligning with the latest best practices and service roadmaps.

This design methodology strives to provide an easy to follow design path to help navigate this complexity and inform design decisions required to produce an optimal target architecture.

## 1&mdash;Design for business requirements

Not all mission-critical workloads have the same requirements. Expect that the review considerations and design recommendations provided by this design methodology will yield different design decisions and trade-offs for different application scenarios.

### Select a reliability tier

Reliability is a relative concept and for any workload to be appropriately reliable it should reflect the business requirements surrounding it. For example, a mission-critical workload with a 99.999% availability Service Level Objective (SLO) requires a much higher level of reliability than another less critical workload with an SLO of 99.9%. 

This design methodology applies the concept of reliability tiers expressed as availability SLOs to inform required reliability characteristics. The table below captures permitted error budgets associated with common reliability tiers.  

|Reliability Tier (Availability SLO)|Permitted Downtime (Week)|Permitted Downtime (Month)|Permitted Downtime (Year)|
|--|--|--|--|
|99.9%|10 minutes, 4 seconds|43 minutes, 49 seconds|8 hours, 45 minutes, 56 seconds|
|99.95%|5 minutes, 2 seconds|21 minutes, 54 seconds|4 hours, 22 minutes, 58 seconds|
|99.99%|1 minutes|4 minutes 22 seconds|52 minutes, 35 seconds|
|99.999%|6 seconds|26 seconds|5 minutes, 15 seconds|
|99.9999%|<1 second|2 seconds|31 seconds|

> [!IMPORTANT]
> Availability SLO is considered by this design methodology to be more than simple uptime, but rather a consistent level of application service relative to a known healthy application state.

As an initial exercise, readers are advised to select a target reliability tier by determining how much downtime is acceptable? The pursuit of a particular reliability tier will ultimately have a significant bearing on the design path and encompassed design decisions, which will result in a different target architecture. 

This image shows how the different reliability tiers and underlying business requirements influence the target architecture for a conceptual reference implementation, particularly concerning the number of regional deployments and utilized global technologies.

![Mission-critical reliability dial](./images/alwayson-slo.gif "Mission-critical reliability dial")

### Consider trade-offs and opportunity cost

There are obvious trade-offs associated with introducing greater reliability, which should be carefully considered in the context of workload requirements.

More specifically, there is an opportunity cost associated with engineering investment in fundamental reliability concepts, such as embracing infrastructure as code, deployment automation, and chaos engineering. This comes at a cost in terms of both time and effort, which could be invested elsewhere to deliver new application functionality and features.

Furthermore, maximizing reliability can impact the overall financial cost of the solution. For example, the duplication of resources and the distribution of resources across regions to achieve high availability has clear cost implications. To avoid excess costs, don't over-engineer or over-provision beyond the relevant business requirements.

## 2&mdash;Refer to the mission-critical implementations

The Mission-Critical open source project strives to illustrate the design recommendations provided by this methodology, and should be used as a source of reference to inform the art of the possible.

> ![GitHub logo](./../_images/github.svg) [ Mission-Critical open source project](http://github.com/azure/alwayson)

There are two foundational [reference implementations](mission-critical-overview.md#illustrative-examples):

  - [Mission-Critical Foundational Online](https://github.com/azure/alwayson-foundational-online)
  - [Mission-Critical Foundational Connected](https://github.com/azure/alwayson-foundational-connected) 


**Production-grade artifacts**: Every technical artifact is ready for use in production environments with all end-to-end operational aspects considered.

**Rooted in customer truth** - All technical decisions are guided by the real-wold experiences of Azure customers and lessons learned from those engagements.

**Azure roadmap alignment** - The mission-critical reference architectures have their own roadmap that is aligned with Azure product roadmaps.

## 3&mdash;Evaluate the design areas using the design principles

At the core of this methodology lies a critical design path comprised of foundational [design principles](mission-critical-design-principles.md) and fundamental [design area](mission-critical-architecture-pattern.md) with heavily interrelated and dependent design decisions.

The impact of decisions made within each design area will reverberate across other design areas and design decisions. Review the provided considerations and recommendations to better understand the consequences of encompassed decisions, which may produce trade-offs within related design areas. 

For example, to define a target architecture it's critical to determine how best to monitor application health across key components. We highly recommend that you review the health modeling design area, using the outlined recommendations to help drive decisions.

## 4&mdash;Deploy a sandbox application environment

In parallel to design activities, it's highly recommended that a sandbox application environment be established using the Azure Mission-Critical reference implementations.

This provides hands-on opportunities to validate design decisions by replicating the target architecture, allowing for design uncertainty to be swiftly assessed. If applied correctly with representative requirement coverage, most problematic issues likely to hinder progress can be uncovered and subsequently addressed.

## Target architecture evolution

Application architectures established using this design methodology must continue to [evolve in alignment with Azure platform roadmaps](/azure/architecture/guide/design-principles/design-for-evolution) to support optimized sustainability.

## Next step

Review the design principles for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Design principles](mission-critical-design-principles.md)
