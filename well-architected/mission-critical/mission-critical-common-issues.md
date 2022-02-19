---
title: Cross-cutting issues of a mission-critical workload
description: Critical cross-cutting concerns and subsequent consideration within each design area.
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

# Cross-cutting issues of a mission-critical workload

There are several cross-cutting concerns that traverse the [key design areas](/mission-critical-overview.md#what-are-the-key-design-areas). This article describes those concerns for subsequent consideration within each design area.

> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [What is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)

## Scale limits

In the cloud, all services have limits in their ability to scale up. Azure service limits are documented in Azure subscription and service limits, quotas, and constraints. Limits include number of cores, database size, query throughput, and network throughput. If your system grows sufficiently large, you may hit one or more of these limits. Use partitioning to work around these limits.

Various [limits and quotas within the Azure platform](/azure/azure-resource-manager/management/azure-subscription-service-limits) may have a significant bearing on a large mission-critical application  and must be appropriately considered by the target architecture.

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
