---
title: Design methodology for mission-critical workloads on Azure
description: Understand the architectural process of building a mature mission-critical application on Microsoft Azure.
author: calcof
ms.author: calcof
ms.date: 03/15/2023
ms.topic: conceptual
categories:
  - management-and-governance
  - web

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

![Mission-critical reliability dial](./images/mission-critical-slo.gif "Mission-critical reliability dial")

Recovery Time Objective (RTO) and Recovery Point Objective (RPO) are further critical aspects when determining required reliability. For instance, if you're striving to achieve an application RTO of less than a minute then back-up based recovery strategies or an active-passive deployment strategy are likely to be insufficient. 

## 2&mdash;Evaluate the design areas using the design principles

At the core of this methodology lies a critical design path comprised of:

- Foundational **[design principles](mission-critical-design-principles.md)**
- Fundamental **[design area](mission-critical-overview.md#what-are-the-key-design-areas)** with heavily interrelated and dependent design decisions.

The impact of decisions made within each design area will reverberate across other design areas and design decisions. Review the provided considerations and recommendations to better understand the consequences of encompassed decisions, which may produce trade-offs within related design areas. 

For example, to define a target architecture it's critical to determine how best to monitor application health across key components. We highly recommend that you review the health modeling design area, using the outlined recommendations to help drive decisions.

## 3&mdash;Deploy your first mission-critical application

Refer to these reference architectures that describe the design decisions based on this methodology.

- [Baseline architecture of an internet-facing application](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro)

- [Baseline architecture of an internet-facing application with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture)

> [!TIP]
> ![GitHub logo](./../_images/github.svg) The architecture is backed by [Mission-Critical Online](https://github.com/Azure/Mission-Critical-Online) implementation that illustrates the design recommendations.

**Production-grade artifacts** Every technical artifact is ready for use in production environments with all end-to-end operational aspects considered.

**Rooted in real-world experiences** All technical decisions are guided by experiences of Azure customers and lessons learned from deploying those solutions.

**Azure roadmap alignment** The mission-critical reference architectures have their own roadmap that is aligned with Azure product roadmaps.

## 4&mdash;Integrate your workload in Azure landing zones

[Azure landing zone subscriptions](/azure/cloud-adoption-framework/ready/landing-zone/) provide shared infrastructure for enterprise deployments that need centralized governance. 

> [!VIDEO 9e05a6bd-7d10-4a83-9436-370a75dc1919]

It's crucial to evaluate which connectivity use case is required by your mission-critical application. Azure landing zones support two main archetypes separated into different Management Group scopes: **Online** or **Corp.** as shown in this image.

[ ![Diagram of Online and Corp. management groups and integration with a mission-critical workload.](./images/mission-critical-landing-zones.gif) ](./images/mission-critical-landing-zones.gif)

### Online subscription

A mission-critical workload operates as an independent solution, without any direct corporate network connectivity to the rest of the Azure landing zone architecture. The application will be further safeguarded through the [policy-driven governance](/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) and will automatically integrate with centralized platform logging through policy.

The [baseline architecture](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro) and [Mission-Critical Online](https://github.com/Azure/Mission-Critical-Online) implementation align with the Online approach. 

### Corp. subscription

When deployed in a Corp. subscription a mission-critical workload depends on the Azure landing zone to provide connectivity resources. This approach allows integration with other applications and shared services. You'll need to design around  some foundational resources, which will exist up-front as part of the shared-service platform. For example, the regional deployment stamp should no longer encompass an ephemeral Virtual Network or Azure Private DNS Zone because these will exist in the Corp. subscription.

To get started with this use case, we recommend the [**baseline architecture in an Azure landing zone**](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-landing-zone) reference architecture. 

> [!TIP]
> ![GitHub logo](./../_images/github.svg) The preceding architecture is backed by [Mission-Critical Connected](https://github.com/Azure/Mission-Critical-Connected) implementation.


## 5&mdash;Deploy a sandbox application environment

In parallel to design activities, it's highly recommended that a sandbox application environment is established using the Mission-Critical reference implementations.

This provides hands-on opportunities to validate design decisions by replicating the target architecture, allowing for design uncertainty to be quickly assessed. If applied correctly with representative requirement coverage, most problematic issues likely to hinder progress can be uncovered and subsequently addressed.

## 6&mdash;Continuously evolve with Azure roadmaps

Application architectures established using this design methodology must continue to [evolve in alignment with Azure platform roadmaps](/azure/architecture/guide/design-principles/design-for-evolution) to support optimized sustainability.

## Next step

Review the design principles for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Design principles](mission-critical-design-principles.md)
