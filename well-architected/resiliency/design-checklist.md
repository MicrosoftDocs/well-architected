---
title: Design for reliability
description: Review a checklist for reliability in application design. Considerations include uptime (availability), high resiliency, low latency, and cost.
author: martinekuan
ms.author: martinek
ms.date: 04/26/2023
ms.topic: conceptual
---

# Design for reliability

Reliable applications should maintain a predefined percentage of uptime, or *availability*. They should also balance between high resiliency, low latency, and cost, which is *high availability*. As important, applications should be able to recover from failures, which is *resiliency*.

## Checklist

How have you designed your applications with reliability in mind?

> [!div class="checklist"]
>
> - Define availability and recovery targets to meet business requirements.
> - Build reliability and availability into your apps by gathering requirements.
> - Ensure that application and data platforms meet your reliability requirements.
> - Configure connection paths to promote availability.
> - Use Azure availability zones where applicable to improve reliability and optimize costs.
> - Ensure that your application architecture is resilient to failures.
> - Know what happens if the requirements of service-level agreements (SLAs) are not met.
> - Identify possible failure points in the system to build reliability.
> - Ensure that applications can operate in the absence of their dependencies.

## Azure services

- [Azure Front Door](/azure/frontdoor/front-door-overview)
- [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)
- [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)
- [Azure NAT Gateway](/azure/virtual-network/nat-gateway/nat-overview)
- [Azure Service Fabric](/azure/service-fabric/service-fabric-overview)
- [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes)
- [Azure Site Recovery](/azure/site-recovery/site-recovery-overview)

## Reference architecture

- [Deploy highly available network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha)
- [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis)
- [Minimize coordination](/azure/architecture/guide/design-principles/minimize-coordination)

## Related Links

- [Use platform as a service (PaaS) options](/azure/architecture/guide/design-principles/managed-services)
- [Design to scale out](/azure/architecture/guide/design-principles/scale-out)
- [Workload availability targets](./business-metrics.md)
- [Building solutions for high availability using availability zones](/azure/architecture/high-availability/building-solutions-for-high-availability)
- [Make all things redundant](/azure/architecture/guide/design-principles/redundancy)

## Next steps

> [!div class="nextstepaction"]
> [Target functional and nonfunctional requirements](./design-requirements.md)
