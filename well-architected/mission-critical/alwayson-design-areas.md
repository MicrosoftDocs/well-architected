---
title: Mission-critical workloads - Critical design areas
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

# Critical design areas

The eight design areas below represent the architecturally significant topics which must be discussed and designed for when defining a target AlwaysOn application architecture. In this regard, this section of the repository is intended to provide prescriptive and opinionated guidance to support readers in designing an AlwaysOn solution.

- [Application Design](./alwayson-application-design.md)
- [Application Platform](./alwayson-application-platform.md)
- [Data Platform](./alwayson-data-platform.md)
- [Health Modeling and Observability](./alwayson-health-modeling.md)
- [Deployment and Testing](./alwayson-deployment-testing.md)
- [Networking and Connectivity](./alwayson-networking-connectivity.md)
- [Security](./alwayson-security.md)
- [Operational Procedures](./alwayson-operational-procedures.md)

These 8 critical design areas will be explored at length within ensuing pages, for which critical review considerations and design recommendations are provided along with their broader design impact across other areas. Ultimately, the design areas are interrelated and decisions made within one area can impact or influence decisions across the entire design, so readers are encouraged to use the provided design guidance to navigate the key design decisions.

![AlwaysOn Design Areas](./images/alwayson-design-areas.png "AlwaysOn Critical Design Areas")

## Reference Architecture

An AlwaysOn application architecture is defined by the various design decisions required to ensure both functional and non-functional business-requirements are fully satisfied. The target AlwaysOn architecture is therefore greatly influenced by the relevant business requirements, and as a result may vary between different application contexts.

The image below represents a target technical state recommended for mission-critical applications on Azure. It leverages a reference set of business requirements to achieve an optimized architecture for different target reliability tiers.

![AlwaysOn Online Foundational Reference Architecture](./images/alwayson-architecture-foundational-online.png "AlwaysOn Online Foundational Reference Architecture")

> The [foundational-online](https://github.com/Azure/AlwaysOn-Foundational-Online) and [foundational-connected](https://github.com/Azure/AlwaysOn-Foundational-Connected) reference implementations provide solution orientated showcases for the AlwaysOn design methodology, demonstrating how this architecture pattern can be implemented alongside the operational wrappers required to maximize reliability and operational effectiveness.

## Cross Cutting Concerns

There are several critical cross-cutting themes which traverse the 8 design areas and are contextualized below for subsequent consideration within each design area.

### Scale limits

Various [limits and quotas within the Azure platform](/azure/azure-resource-manager/management/azure-subscription-service-limits) may have a significant bearing on large AlwaysOn application scenarios and must be appropriately considered by the target architecture.

> Limits and quotas may change as Azure seeks to further enhance the platform and user experience.

- Leverage subscriptions as scale units, scaling out resources and subscriptions as required
- Employ a scale unit approach for resource composition, deployment, and management
- Ensure scale limits are considered as part of capacity planning
- If available, use data gathered about existing application environments to explore which limits might be encountered

### Automation

Maximize reliability and operability through the holistic automation of all deployment and management activities.

- Automate CI/CD deployments for all application components
- Automate application management activities, such as patching and monitoring
- Use declarative management semantics over imperative
- Prioritize templating over scripting; only use scripting when it is not possible to use templates

### Azure roadmap alignment and regional service availability

Align the target architecture with the Azure platform roadmap to inform the application trajectory, and ensure that required services and features are available within the chosen deployment regions.

- Align with Azure engineering roadmaps and regional role out plans
- Unblock with preview services or by taking dependencies on the Azure platform roadmap
- Only take a dependency on committed services and features; validate roadmap dependencies with Microsoft engineering product groups

### Azure Landing Zone integration

[Azure Landing Zones](/azure/cloud-adoption-framework/ready/landing-zone/) provides prescriptive architectural guidance to define a reliable and scalable shared-service platform for enterprise Azure deployments with requisite centralised governance. 

AlwaysOn can integrate seamlessly within an Azure Landing Zone, and is deployable within both the *Online* or *Corp. Connected* Landing Zone formats as demonstrated within the image below.

![AlwaysOn and Landing Zone Integration](./images/alwayson-landing-zones.gif "AlwaysOn Landing Zone Integration")

It is crucial to understand and identify in which connectivity scenario an AlwaysOn application requires since Azure Landing Zones support different landing zones archetypes separated into different Management Group scopes.

- In the context of an *Online* Landing Zone archetype, AlwaysOn operates as a completely independent solution, without any direct corporate network connectivity to the rest of the Azure Landing Zone architecture. The application will, however, be further safeguarded through the [*policy-driven management*](/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) approach which is foundational to Azure Landing Zones, and will automatically integrate with centralized platform logging through policy.

  - A *Online* deployment can only really consider a public AlwaysOn application deployment since there is no private corporate connectivity provided.

- When deployed in a *Corp. Connected* Landing Zone context, the AlwaysOn application takes a dependency on the Azure Landing Zones platform to provide connectivity resources which allow for integration with other applications and shared services existing on the platform. This necessitates some transformation on-top of the *Online* integration approach, since some foundational resources are expected to exist up-front as part of the shared-service platform. More specifically, the AlwaysOn regional deployment stamp should not longer encompass an ephemeral Virtual Network or Azure Private DNS Zone since these will exist within the Azure Landing Zones *connectivity* subscription. 
  - A *Corp. Connected* deployment can consider both a public or private AlwaysOn application deployment.

> The AlwaysOn reference implementations are fully aligned with the Azure Landing Zones architectural approach and are immediately deployable within an *Online* or *Connected* Landing Zone subscription.

## Next steps

- Review all 8 critical design areas to inform key design decisions surrounding the definition of a target architecture.
  - [Application Design](./alwayson-application-design.md)
  - [Application Platform](./alwayson-application-platform.md)
  - [Data Platform](./alwayson-data-platform.md)
  - [Health Modeling and Observability](./alwayson-health-modeling.md)
  - [Deployment and Testing](./alwayson-deployment-testing.md)
  - [Networking and Connectivity](./alwayson-networking-connectivity.md)
  - [Security](./alwayson-Security.md)
  - [Operational Procedures](./alwayson-operational-procedures.md)

### Reference implementation

- Use the foundational reference implementations to construct a synthetic application environment that can be used to validate key design decisions. 
  - [Foundational-Online reference implementation](https://github.com/azure/alwayson-foundational-online) on GitHub.
  - [Foundational-Connected reference implementation](https://github.com/azure/alwayson-foundational-connected) on GitHub.
