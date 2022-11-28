---
title: Architecture pattern for mission-critical workloads on Azure
description: The design areas represent the architecturally significant topics that must be discussed and designed for when defining a target architecture
author: calcof
ms.author: calcof
ms.date: 08/15/2022
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
---

# Architecture pattern for mission-critical workloads on Azure

This section provides design considerations and recommendations across architecturally significant topics that are relevant for a mission-critical workload. The design areas are interrelated and decisions made within one area can impact or influence decisions across the entire design.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)
>
> ![GitHub logo](./../_images/github.svg) [Mission-Critical open source project](http://github.com/azure/mission-critical)
>
> There are [reference implementations](mission-critical-overview.md#illustrative-examples) available as part of an open source project on GitHub. The code assets provided by these implementations illustrate the recommendations highlighted in this article.

## Reference architecture

A mission-critical workload architecture is defined by the various design decisions required to ensure both functional and non-functional business-requirements are fully satisfied. The target architecture is therefore greatly influenced by the relevant business requirements, and as a result may vary between different application contexts.

### Baseline architecture
The image below represents a baseline reference architecture recommended for mission-critical workloads on Azure. It leverages a reference set of business requirements to achieve an optimized architecture for different target reliability tiers. The architecture is designed for a workload that is accessed over a public endpoint and does not require private network connectivity to other company resources.

![Mission-critical baseline reference architecture](/azure/architecture/reference-architectures/containers/aks-mission-critical/images/mission-critical-architecture-online.png)

> [Guidance for this reference architecture](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro).

### Baseline architecture with network controls

This image shows the baseline architecture with strict controls to stop attack vectors at the networking layer so that the overall reliability of the system isn't impacted. 

![Mission-critical baseline reference architecture with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/images/mission-critical-architecture-network.svg)

> [Guidance for this reference architecture](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture).


### Baseline architecture in Azure landing zones

The [Baseline architecture in an Azure landing zone](https://learn.microsoft.com/en-us/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-landing-zone) reference architecture aligns with the Corp. archetype.


The [Mission-Critical Online](https://github.com/Azure/Mission-Critical-Online) and [Mission-Critical Connected](https://github.com/Azure/Mission-Critical-Connected) provide solution orientated showcases for this use case. 

## Design areas
We recommend that you use the provided design guidance to navigate the key design decisions to reach an optimal solution. For information, see [What are the key design areas?](/azure/architecture/framework/mission-critical/mission-critical-overview#what-are-the-key-design-areas)


## Next step

Review the best practices for architecting mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Application design](./mission-critical-application-design.md)

