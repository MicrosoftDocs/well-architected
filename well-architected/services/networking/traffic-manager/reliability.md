---
title: Reliability and Traffic Manager
description: Focuses on the Traffic Manager service used in the Networking solution to provide best-practice and configuration recommendations related to Reliability.
author: v-stacywray
ms.author: robbymillsap
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

# Reliability and Traffic Manager

[Traffic Manager](/azure/traffic-manager/) is a Domain Name System (DNS)-based traffic load balancer. This service allows you to distribute traffic to your public-facing applications across the global Azure regions. Traffic Manager also provides your public endpoints with high availability and quick responsiveness.

Features include:

- [Increase application availability](/azure/traffic-manager/traffic-manager-overview#increase-application-availability)
- [Improve application performance](/azure/traffic-manager/traffic-manager-overview#improve-application-performance)
- [Service maintenance without downtime](/azure/traffic-manager/traffic-manager-overview#service-maintenance-without-downtime)
- [Combine hybrid applications](/azure/traffic-manager/traffic-manager-overview#combine-hybrid-applications)
- [Distribute traffic for complex deployments](/azure/traffic-manager/traffic-manager-overview#distribute-traffic-for-complex-deployments)

For more information, reference [What is Traffic Manager?](/azure/traffic-manager/traffic-manager-overview)

To learn how Traffic Manager supports a reliable workload, reference the following topics:

- [Enhance your service availability and data locality by using Azure Traffic Manager](/learn/modules/distribute-load-with-traffic-manager/)
- [Using load-balancing services in Azure](/azure/traffic-manager/traffic-manager-load-balancing-azure)
- [Disaster recovery using Azure DNS and Traffic Manager](/azure/networking/disaster-recovery-dns-traffic-manager)

## Checklist

**Have you configured Traffic Manager with reliability in mind?**

> [!div class="checklist"]
> - If the Time to Live (TTL) interval of the DNS record is too long, consider adjusting the health probe timing or DNS record TTL.
> - Implement a custom page to use as a health check for your Traffic Manager.
> - Evaluate the three different traffic routing methods.
> - Consider nested Traffic Manager profiles.

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring Traffic Manager:

|Recommendation|Description|
|--------------|-----------|
|If the Time to Live (TTL) interval of the DNS record is too long, consider adjusting the health probe timing or DNS record TTL.|When a backend becomes unavailable, Traffic Manager won't fail over to another region immediately. There will be a time interval where clients can't be served. The length of this interval depends on the time settings of the health probe (probe interval and the number of unhealthy responses allowed). If the resulting interval is still too large for the scenario, consider switching to Azure Front Door for global load balancing.|
|Implement a custom page to use as a health check for your Traffic Manager.|A common practice is to implement a custom page within your application (for example: `/health.aspx`). Using this path for monitoring, you can do application-specific checks, such as checking performance counters or verifying database availability. Based on these custom checks, the page returns an appropriate `HTTPS` status code.|
|Evaluate the three different traffic routing methods.|Traffic Manager supports three traffic-routing methods to determine how to route network traffic to the various service endpoints. Traffic Manager applies the traffic-routing method to each DNS query it receives. The traffic-routing method determines which endpoint is returned in the DNS response. The customer should be aware of these endpoints and the differences in routing between endpoints.|
|Consider nested Traffic Manager profiles.|Each Traffic Manager profile specifies a single traffic-routing method. There are scenarios that require more sophisticated traffic routing than the routing provided by a single Traffic Manager profile. You can nest Traffic Manager profiles to combine the benefits of more than one traffic-routing method. Nested profiles allow you to override the default Traffic Manager behavior to support larger, more complex application deployments.|

## Next step

> [!div class="nextstepaction"]
> [Operational excellence and Traffic Manager](operational-excellence.md)
