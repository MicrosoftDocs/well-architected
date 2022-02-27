---
title: Cross-cutting concerns of a mission-critical workload
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

# Cross-cutting concerns of a mission-critical workload

There are several cross-cutting concerns that traverse the [key design areas](mission-critical-overview.md#what-are-the-key-design-areas). This article provides recommendations for the common concerns. 

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [What is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)
>
> ![GitHub logo](./../_images/github.svg) [AlwaysOn open source project](http://github.com/azure/alwayson)
>
> The [reference implementations](mission-critical-overview.md#illustrative-examples) are part of an open source project available on GitHub. The code assets illustrate the implementations associated with the design principles highlighted in this article.

## Scale limits

In Azure, all services have _limits_ or _quotas_ in their ability to scale up. Examples of limits include restrictions on number of instances in a subscription, database size, query throughput, network throughput, and others. 

Service limits may have a significant bearing on a large mission-critical workload. Consider the limits of the services used in the target architecture carefully. Otherwise, you may hit one or more of these limits as the workload grows.

> [!IMPORTANT]
> Limits and quotas may change as the platform evolves. Be sure to check the current limits in [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits).

### Recommendations

- Use subscriptions as scale units. Scale out resources and subscriptions as required.
- Employ a scale unit approach for resource composition, deployment, and management.
- Ensure scale limits are considered as part of capacity planning.
- If available, use data about existing application environments to explore which limits might be encountered.

## Automation

A holistic approach to automation of deployment and management activities can maximize the reliability and operability of the workload.

### Recommendations

- Automate continuous integration, continuous delivery (CI/CD) pipelines for all application components.
- Automate application management activities, such as patching and monitoring.
- Use declarative management semantics, such as Infrastructure as code (IaC), instead of over imperative approaches.
- Prioritize templating over scripting. Defer to scripting only when using templates isn't possible.

## Azure roadmap alignment

Azure is constantly evolving and there are frequent updates to services, features, and regional availability. Keeping track of the updates might help you make informed decisions about the application trajectory. For example, making sure that the required services and features are available within the chosen deployment regions.

See [Azure updates](https://azure.microsoft.com/updates/) for latest information about services and features. 

### Recommendations

- Align with Azure engineering roadmaps and regional rollout plans.
- Unblock with preview services or by taking dependencies on the Azure platform roadmap.
- Only take a dependency on committed services and features; validate roadmap dependencies with Microsoft engineering product groups.

## Next step

Review each design area associated with a  mission-critical application.

> [!div class="nextstepaction"]
> [Architecture pattern](mission-critical-architecture-pattern.md)
