---
title: Operational excellence and Traffic Manager
description: Focuses on the Traffic Manager service used in the Networking solution to provide best-practice and configuration recommendations related to Operational excellence.
author: v-stacywray
ms.author: martinekuan
ms.date: 01/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - traffic-manager
categories:
  - networking
  - management-and-governance
---

# Operational excellence and Traffic Manager

[Traffic Manager](/azure/traffic-manager/) is a Domain Name System (DNS)-based traffic load balancer. This service allows you to distribute traffic to your public-facing applications across the global Azure regions. Traffic Manager also provides your public endpoints with high availability and quick responsiveness.

Features include:

- [Increase application availability](/azure/traffic-manager/traffic-manager-overview#increase-application-availability)
- [Improve application performance](/azure/traffic-manager/traffic-manager-overview#improve-application-performance)
- [Service maintenance without downtime](/azure/traffic-manager/traffic-manager-overview#service-maintenance-without-downtime)
- [Combine hybrid applications](/azure/traffic-manager/traffic-manager-overview#combine-hybrid-applications)
- [Distribute traffic for complex deployments](/azure/traffic-manager/traffic-manager-overview#distribute-traffic-for-complex-deployments)

For more information, reference [What is Traffic Manager?](/azure/traffic-manager/traffic-manager-overview)

To learn how Traffic Manager supports operational excellence, reference the following articles:

- [Troubleshooting degraded state on Azure Traffic Manager](/azure/traffic-manager/traffic-manager-troubleshooting-degraded)
- [Traffic Manager endpoint monitoring](/azure/traffic-manager/traffic-manager-monitoring)
- [Traffic Manager metrics and alerts](/azure/traffic-manager/traffic-manager-metrics-alerts)

## Checklist

**Have you configured Traffic Manager with operational excellence in mind?**

> [!div class="checklist"]
> - If the Time to Live (TTL) interval of the DNS record is too long, consider adjusting the health probe timing or DNS record TTL.

## Configuration recommendations

Consider the following recommendation for operational excellence when configuring Traffic Manager:

|Recommendation|Description|
|--------------|-----------|
|If the Time to Live (TTL) interval of the DNS record is too long, consider adjusting the health probe timing or DNS record TTL.|When a backend becomes unavailable, Traffic Manager won't fail over to another region immediately. There will be a time interval where clients can't be served. The length of this interval depends on the time settings of the health probe (probe interval and the number of unhealthy responses allowed). If the resulting interval is still too large for the scenario, consider switching to Azure Front Door for global load balancing.|

## Next step

> [!div class="nextstepaction"]
> [Cost optimization and IP addresses](../ip-addresses/cost-optimization.md)
