---
title: Application Delivery (General) and operational excellence
description: Focuses on the Application Delivery (General) service used in the Networking solution to provide best-practice, configuration recommendations, and design considerations related to Operational excellence.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/20/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - application-delivery
categories:
  - networking
  - management-and-governance
---

# Application Delivery (General) and operational excellence

Application Delivery (General) explores key recommendations to deliver internal-facing and external-facing applications in the Azure network through a secure, highly scalable, and highly available way. General Application Delivery can be handled using a combination of the following networking services in Azure:

- [Content Delivery Network (CDN)](/azure/cdn/cdn-overview)
- [Azure Front Door Service](/azure/frontdoor/front-door-overview)
- [Traffic Manager](/azure/traffic-manager/traffic-manager-overview)
- [Application Gateway](/azure/application-gateway/overview)
- [Internet Analyzer](/azure/internet-analyzer/internet-analyzer-overview)
- [Load Balancer](/azure/load-balancer/load-balancer-overview)

For more information, reference [Azure networking services overview](/azure/networking/fundamentals/networking-overview#deliver).

The following sections are specific to general Application Delivery and operational excellence:

- Design considerations
- Configuration checklist
- Recommended configuration options

## Design considerations

Application Delivery in Azure includes the following design considerations:

- Azure Load Balancer (internal and public) provides high availability for application delivery at a regional level. (*Standard tier only*)
- Azure Traffic manager allows the delivery of applications through DNS redirection, including traffic using protocols other than `HTTP/S`.
- Azure Front Door allows the secure delivery of highly available `HTTP/S` applications across Azure regions.
- Azure Application Gateway allows the secure delivery of `HTTP/S` applications at a regional level.

## Checklist

**Have you configured your Application Delivery networking services with operational excellence in mind?**

> [!div class="checklist"]
> - Use Azure Traffic Manager to deliver global applications that span protocols other than `HTTP/S`.
> - When using Azure Front Door and Application Gateway to protect `HTTP/S` applications, use Web Application Firewall (WAF) policies in Front Door and lock down Application Gateway to receive traffic only from Azure Front Door.
> - Create a separate health endpoint on the backend that the health probe can use. The health endpoint can aggregate the state of the critical services and dependencies needed to serve requests.
> - Enable health probes for backends.
> - Deploy Application Gateway v2 or third-party NVAs used for inbound `HTTP/S` connections together with the applications that they're securing.
> - When doing global load balancing for `HTTP/S` applications, use Front Door over Traffic Manager.
> - Use a third-party Network Virtual Appliance (NVA) if Application Gateway v2 can't be used for the security of `HTTP/S` applications.
> - For secure delivery of `HTTP/S` applications, ensure you enable Web Application Firewall (WAF) protection and policies.
> - Application delivery for both internal and external facing applications should be part of the application.
> - Global `HTTP/S` applications that span Azure regions should be delivered and protected using Azure Front Door with Web Application Firewall (WAF) policies.
> - All public IP addresses in the solution should be protected with a DDoS Standard protection plan.

## Configuration recommendations

Consider the following recommendations for operational excellence when configuring your Application Delivery networking services:

|Recommendation|Description|
|--------------|-----------|
|Use Azure Traffic Manager to deliver global applications that span protocols other than `HTTP/S`.|Traffic manager doesn't forward traffic, but only completes the DNS redirection. The connection from the client is established directly to the target using any protocol.|
|When using Azure Front Door and Application Gateway to protect `HTTP/S` applications, use WAF policies in Front Door and lock down Application Gateway to receive traffic only from Azure Front Door.|Certain scenarios might force a customer to implement rules specifically on AppGateway: For example, if ModSec CRS `2.2.9`, CRS `3.0` or CRS `3.1` rules are required, rules can be only implemented on AppGateway. Conversely, rate-limiting and geo-filtering are available only on Azure Front Door, not on AppGateway. Instructions on how to lock down traffic can be found at [FAQ for Azure Front Door](/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door).|
|Create a separate health endpoint on the backend that the health probe can use. The health endpoint can aggregate the state of the critical services and dependencies needed to serve requests.|For more information, reference [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).|
|Enable health probes for backends.|Health probes are `http(s)` endpoints that are queried by the load balancer (Azure Front Door, Traffic Manager, AppGateway) service to determine if the backend is healthy enough to handle requests.|
|Deploy Application Gateway v2 or third-party NVAs used for inbound `HTTP/S` connections together with the applications that they're securing.|Don't centrally manage Application Gateway v2 or third-party NVAs within the organization and share with other workloads.|
|When doing global load balancing for `HTTP/S` applications, use Front Door over Traffic Manager.|- Azure Front Door optimizes the number of TCP connections to the backend when forwarding traffic.<br>- Changes to the routing configuration, based on backend health, are instantaneous. With Traffic Manager, traffic will point to the original backend until a new DNS lookup occurs, plus potential time for DNS propagation.<br>- Front Door supports caching on global edge nodes, negating the need for a separate CDN service.<br>- Front Door supports Web Application Firewall rules, negating the need for a separate WAF service.|
|For secure delivery of `HTTP/S` applications, ensure you enable Web Application Firewall (WAF) protection and policies.|Enable WAF protection and policies in either Application Gateway or Front Door.|
|Application delivery for both internal and external facing applications should be part of the application.|Don't centrally manage Application Delivery within an organization.|

## Next step

> [!div class="nextstepaction"]
> [Azure Application Gateway v2 and reliability](reliability.md)