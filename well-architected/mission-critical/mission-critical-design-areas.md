---
title: Design areas for a mission-critical workload on Azure
description: The design areas represent the architecturally significant topics which must be discussed and designed for when defining a target architecture
author: calcof
ms.author: calcof
ms.date: 02/02/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - databases
  - web
  - devops
  - security
  - networking
products: Azure
ms.custom:
  - mission-critical
  - alwayson
---

# Design areas for mission-critical workloads

This section provides design considerations and recommendations across architecturally significant topics that are relevant for a mission-critical workload. These design areas are interrelated and decisions made within one area can impact or influence decisions across the entire design.

- [Application Design](./mission-critical-application-design.md)
- [Application Platform](./mission-critical-application-platform.md)
- [Data Platform](./mission-critical-data-platform.md)
- [Health Modeling and Observability](./mission-critical-health-modeling.md)
- [Deployment and Testing](./mission-critical-deployment-testing.md)
- [Networking and Connectivity](./mission-critical-networking-connectivity.md)
- [Security](./mission-critical-security.md)
- [Operational Procedures](./mission-critical-operational-procedures.md)

We recommend that you use the provided design guidance to navigate key design decisions and support you with designing an optimal solution.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you are not familiar with this series, we recommend you start with [What is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload).
>
> ![GitHub logo](./../_images/github.svg) [Azure Mission-Critical open source project](http://github.com/azure/alwayson)
>
> There are [reference implementations](mission-critical-overview.md#illustrative-examples) available as part of an open source project on GitHub. The code assets provided by these implementations illustrate the recommendations highlighted in this article.

## Reference architecture

A mission-critical workload architecture is defined by the various design decisions required to ensure both functional and non-functional business-requirements are fully satisfied. The target architecture is therefore greatly influenced by the relevant business requirements, and as a result may vary between different application contexts.


The image below represents a reference architecture recommended for mission-critical workloads on Azure. It leverages a reference set of business requirements to achieve an optimized architecture for different target reliability tiers.

![Mission-critical online reference architecture](./images/alwayson-architecture-foundational-online.png "ission-critical online reference architecture")

>The [online reference implementation](https://github.com/azure/alwayson-foundational-online) and [connected reference implementation](https://github.com/azure/alwayson-foundational-connected) provide solution orientated showcases for this design methodology, demonstrating how this architecture pattern can be implemented alongside the operational wrappers required to maximize reliability and operational effectiveness.

Use these reference implementations to construct a sandbox application environment for validating key design decisions.

### Azure Landing Zone integration

[Azure Landing Zones](/azure/cloud-adoption-framework/ready/landing-zone/) provides prescriptive architectural guidance to define a reliable and scalable shared-service platform for enterprise Azure deployments with requisite centralised governance. 

This mission-critical workload series provides prescriptive architectural guidance to define a highly-reliable application for mission-critical workloads that could be deployed within an Azure Landing Zone.

The mission-critical [reference implementations](mission-critical-overview.md#illustrative-examples) can integrate seamlessly within an Azure Landing Zone, and is deployable within both the *Online* or *Corp. Connected* Landing Zone formats as demonstrated within the image below.

![Mission-critical workload and Landing Zone integration](./images/alwayson-landing-zones.gif "Mission-critical workload and Landing Zone integration")

It is crucial to understand and identify in which connectivity scenario a mission-critical application requires since Azure Landing Zones support different landing zones archetypes separated into different Management Group scopes.

- In the context of an *Online* Landing Zone archetype, a mission-critical workload operates as a completely independent solution, without any direct corporate network connectivity to the rest of the Azure Landing Zone architecture. The application will, however, be further safeguarded through the [*policy-driven management*](/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) approach which is foundational to Azure Landing Zones, and will automatically integrate with centralized platform logging through policy.

  - A *Online* deployment can only really consider a public application deployment since there is no private corporate connectivity provided.

- When deployed in a *Corp. Connected* Landing Zone context, the mission-critical workload takes a dependency on the Azure Landing Zones platform to provide connectivity resources which allow for integration with other applications and shared services. This necessitates some transformation on-top of the *Online* integration approach, since some foundational resources are expected to exist up-front as part of the shared-service platform. More specifically, the regional deployment stamp should not longer encompass an ephemeral Virtual Network or Azure Private DNS Zone since these will exist within the Azure Landing Zones *connectivity* subscription. 

  - A *Corp. Connected* deployment can consider both a public or private AlwaysOn application deployment.

> The mission-critical reference implementations are fully aligned with the Azure Landing Zones architectural approach and are immediately deployable within an *Online* or *Connected* Landing Zone subscription.

## Next step

Review the best practices for architecting mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Application Design](./mission-critical-application-design.md)
