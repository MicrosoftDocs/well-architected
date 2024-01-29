---
title: Azure Well-Architected Framework - Azure Front Door
description: Design considerations and recommendations about Azure Front Door.
author: duongau
ms.author: prwilk
ms.date: 01/28/2024
ms.topic: conceptual
products:
  - azure-front-door
categories:
  - networking
---
# Azure Well-Architected Framework perspective on Azure Front Door

Azure Front Door is a Content Delivery Network (CDN) and a global load balancer suitable for HTTP(S) traffic with capabilties to deliver and distribute traffic closest to the application users. 

This article assumes that as an architect, you've reviewed the [**Load balancing options**](/azure/architecture/guide/technology-choices/load-balancing-overview) and chosen Azure Front Door as your load balancer. Most likely your application is deployed to multiple regions in an active-active or active-passive model. The guidance in this article provides architectural recommendations that are mapped to the principles of the [**Azure Well-Architected Framework pillars**](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Front Door. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demostrates the key recommendations: [Mission-critical baseline architecture on Azure](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro).

##### Technology scope

This review focuses on the interrelated decisions for these Azure resources.  

- Azure Front Door (classic)
- Azure CDN Standard Microsoft


## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the tiers and CDN capabilities. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Consider a redundant traffic management**.  Front Door is a globally distributed service that runs as a singleton in the environment. **Front Door is a potential single point of point in the system**, this means if the service fails, clients won't be able to access your application during the downtime. 
>   
>   Redundant implementations of this nature can be complex and costly. It should only be consiered for mission-critical workloads that have very low tolerance to outage. Carefully consider the [tradeoffs](/azure/architecture/guide/networking/global-web-applications/overview#tradeoffs). 
>   - If you absolutely need redundant routing, see [**Global routing redundancy**](hazure/architecture/guide/networking/global-web-applications/overview).
>   - If need redundancy only to serve cached content, see [**Global content delivery**](/azure/architecture/guide/networking/global-web-applications/mission-critical-content-delivery).
> 
> - **Evaluate the resiliency features**. Azure Front Door provides high availability capabilities. It provides various routing methods that determines how traffic is distibuted amongst origins. //TODO
>
> - **Implement the health endpoint monitoring pattern**. Your **application should expose health endpoints**, which  aggregate the state of the critical services and dependencies needed to serve requests. Front Door health probes can then use the endpoint to detect health of the backend servers. For more information, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).

#### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Backend address of a frontDoor load balancer. <br><br>In a backend pool you specify all the locations for a given app endpoint (each called a backend). For availability, deploy backends across multiple zones and regions.||
|Priority to use for load balancing. Higher priorities will not be used for load balancing if any lower priority backend is healthy.|Can be used to implement Active/Passive using Priority based load balancing. <br>A combination of priority and weight can be used, the decision tree is described here: https://learn.microsoft.com/en-us/azure/frontdoor/routing-methods#overall-decision-flow|
|Set up health probes. <br> Disable health probes when there is only one origin|Configuration for the health probes for a given backend pool|
|Set a timeout on forwarding request to the backend. When timeout is reached, the request fails and returns. <br> Make sure timeout is set to the right value for the workload, or it may result in error responses.  The default timeout is 30 seconds.||
|Use the same domain name on Front Door and your origin (host name)||
|Evaluate if [**session affinity**](/azure/frontdoor/routing-methods#23session-affinity) is required. <br> Some applications need the user connections to stay on the same origin server during the user session. If they don't, the application might not work properly. You should verify if your endpoints need session affinity for reliable performance. For more information, see Session affinity. ||