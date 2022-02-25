---
title: Architecture pattern for a  mission-critical application on Azure
description: The design areas represent the architecturally significant topics which must be discussed and designed for when defining a target AlwaysOn application architecture
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

# Architecture pattern for a  mission-critical application on Azure

This section discusses design considerations and recommendations that are significant for  a target mission-critical application architecture. The design areas are interrelated and decisions made within one area can influence decisions across the entire design. We recommend that you take a holistic approach and assess the impact of one area on other key decisions.

- [Application Design](./mission-critical-application-design.md)
- [Application Platform](./mission-critical-application-platform.md)
- [Data Platform](./mission-critical-data-platform.md)
- [Health Modeling and Observability](./mission-critical-health-modeling.md)
- [Deployment and Testing](./mission-critical-deployment-testing.md)
- [Networking and Connectivity](./mission-critical-networking-connectivity.md)
- [Security](./mission-critical-security.md)
- [Operational Procedures](./mission-critical-operational-procedures.md)

## Reference architecture

The AlwaysOn reference implementations are part of an open source project available on GitHub.  

![GitHub logo](./../_images/github.svg) [AlwaysOn open source project](http://github.com/azure/alwayson).

An AlwaysOn application architecture is defined by the various design decisions required to ensure both functional and non-functional business-requirements are fully satisfied. The target AlwaysOn architecture is therefore greatly influenced by the relevant business requirements, and as a result may vary between different application contexts.

Here's a reference architecture recommended for mission-critical applications on Azure. It leverages a reference set of business requirements to achieve an optimized architecture for different target reliability tiers.

![AlwaysOn Online Foundational Reference Architecture](./images/alwayson-architecture-foundational-online.png "AlwaysOn Online Foundational Reference Architecture")

[Foundational-Online reference implementation](https://github.com/azure/alwayson-foundational-online) and [Foundational-Connected reference implementation](https://github.com/azure/alwayson-foundational-connected) reference implementations provide solution orientated showcases for the design methodology, demonstrating how this architecture pattern can be implemented alongside the operational wrappers required to maximize reliability and operational effectiveness.

Use the reference implementations to construct a sandbox application environment that can be used to validate key design decisions. 

### Azure Landing Zone integration

[Azure Landing Zones](/azure/cloud-adoption-framework/ready/landing-zone/) provides prescriptive architectural guidance to define a reliable and scalable shared-service platform for enterprise Azure deployments with requisite centralised governance. 

The AlwaysOn reference implementations can integrate seamlessly within an Azure Landing Zone, and is deployable within both the *Online* or *Corp. Connected* Landing Zone formats as demonstrated within the image below.

![AlwaysOn and Landing Zone Integration](./images/alwayson-landing-zones.gif "AlwaysOn Landing Zone Integration")

It is crucial to understand and identify in which connectivity scenario an AlwaysOn application requires since Azure Landing Zones support different landing zones archetypes separated into different Management Group scopes.

- In the context of an *Online* Landing Zone archetype, AlwaysOn operates as an independent solution, without any direct corporate network connectivity to the rest of the Azure Landing Zone architecture. The application will, however, be further safeguarded through the [*policy-driven management*](/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) approach which is foundational to Azure Landing Zones, and will automatically integrate with centralized platform logging through policy.

  - A *Online* deployment can only consider a public AlwaysOn application deployment because there is no private corporate connectivity provided.

- When deployed in a *Corp. Connected* Landing Zone context, the AlwaysOn application takes a dependency on the Azure Landing Zones platform to provide connectivity resources which allow for integration with other applications and shared services existing on the platform. This necessitates some transformation on-top of the *Online* integration approach, since some foundational resources are expected to exist up-front as part of the shared-service platform. More specifically, the AlwaysOn regional deployment stamp should not longer encompass an ephemeral Virtual Network or Azure Private DNS Zone since these will exist within the Azure Landing Zones *connectivity* subscription. 
  - A *Corp. Connected* deployment can consider both a public or private AlwaysOn application deployment.

> The AlwaysOn reference implementations are fully aligned with the Azure Landing Zones architectural approach and are immediately deployable within an *Online* or *Connected* Landing Zone subscription.

## Next step

Review the best practices for operational procedures for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Application Design](./mission-critical-application-design.md)

