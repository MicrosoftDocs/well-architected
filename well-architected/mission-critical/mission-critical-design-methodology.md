---
title: Design methodology for mission-critical workloads on Azure
description: Understand the architectural process of building a mature mission-critical application on Microsoft Azure.
author: calcof
ms.author: calcof
ms.date: 03/15/2023
ms.topic: conceptual
---

# Design methodology for mission-critical workloads on Azure

Designing a mission-critical application on any cloud platform demands deep technical expertise and a thoughtful engineering approach. The complexity spans multiple dimensions: understanding the platform's capabilities, selecting the right services, configuring them correctly, operationalizing them effectively, and staying aligned with evolving best practices and service roadmaps.

To navigate this complexity, establish a clear and simple design methodology that aligns with your business requirements, particularly around uptime and recovery. When decision-making becomes challenging or you find yourself stuck in analysis paralysis, return to your methodology as a reference point. It can help validate your choices, keep your design focused, and ensure alignment with your overall goals.

This article suggests a design methodology grounded in the Azure Well-Architected Framework. It's informed by insights gained from reviewing numerous mission-critical applications deployed on Azure across various organizations.

## Design for your reliability objectives


Mission citical doesn't mean the same for everyone. They vary in their reliability needs, which should be defined by business requirements and acceptable downtime. Selecting a target reliability tier, such as 99.9% vs. 99.999% availability, directly influences architectural decisions and tradeoffs. 

This design methodology encourages tailoring your architecture to the specific Service Level Objective (SLO). Consider availability SLOs to be more than just uptime. They represent consistent service relative to a healthy application state. As a starting point, teams should define how much downtime is acceptable. 

|Reliability (SLO)|Permitted Downtime (Week)|Permitted Downtime (Month)|Permitted Downtime (Year)|
|--|--|--|--|
|99.9%|10 minutes, 4 seconds|43 minutes, 49 seconds|8 hours, 45 minutes, 56 seconds|
|99.95%|5 minutes, 2 seconds|21 minutes, 54 seconds|4 hours, 22 minutes, 58 seconds|
|99.99%|1 minutes|4 minutes 22 seconds|52 minutes, 35 seconds|
|99.999%|6 seconds|26 seconds|5 minutes, 15 seconds|
|99.9999%|<1 second|2 seconds|31 seconds|

While a single-region, multi-zone setup may suffice for many critical workloads, higher reliability tiers demand significantly more engineering effort and complexity. Avoid defaulting to overly complex solutions like active-active multi-region unless truly necessary.

![Mission-critical reliability dial](./images/mission-critical-slo.gif "Mission-critical reliability dial")

RTO (Recovery Time Objective) and RPO (Recovery Point Objective) are key to defining reliability needs. For example, if your goal is to recover an application in under a minute, backup-based or active-passive strategies likely won't be fast enough.

> Refer to [Recommendations for defining reliability targets](../reliability/metrics.md)

## Design for zer-downtime deployments

Zero-downtime deployments ensure users don't experience any disruption during updates. 

This methodology demands rigorous pre-release testing so that updates don't introduce defects, vulnerabilities, or instability. To support this, deployment tools and processes must be highly available and resilient. 

Consistency is also key: the same artifacts and automated processes should be used across all environments to eliminate any chance of manual errors and reduce overall risk. End-to-end automation is not just preferred; it's mandatory for achieving reliable, repeatable, and interruption-free deployments.

> Refer to [Recommendations for deployment and testing](./mission-critical-deployment-testing.md)



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
