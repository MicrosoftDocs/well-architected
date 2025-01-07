---
title: Well-Architected Framework Perspective on Azure Traffic Manager
description: This article provides a Well-Architected Framework perspective on Azure Traffic Manager.
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: conceptual
ms.date: 1/9/2025
ms.service: azure-waf
ms.subservice: waf-service-guide
products: azure-traffic-manager
---

# Well-Architected Framework perspective on Azure Traffic Manager

Traffic Manager is a global load balancer that distributes traffic across multiple Azure regions. It uses DNS to establish a communication path between the client and your workload's endpoints. Once the connection is established, Traffic Manager doesn't interfere, allowing clients to connect directly to the endpoint. 

This article assumes that as an architect, you've reviewed the [load-balancing options in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview) and chose Azure Traffic Manager for your workload, which is deployed across multiple regions in either an active-active or active-passive model. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Azure Traffic Manager and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments. 
>
> Foundational architecture that demonstrates the key recommendations: [Multiregion load balancing with Traffic Manager, Azure Firewall, and Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resource:  

- Azure Traffic Manager

> [!NOTE]
>
> For workloads that host HTTP applications, Azure Front Door is a natural choice because of its features such as CDN, TLS termination, integrated firewall. 
>
> Compared to Azure Front Door, Azure Traffic Manager is easier to set up, configure, and maintain. Azure Traffic Manager doesn't have an endpoint that you can control directly. Unlike Front Door, which handles client requests, Traffic Manager only connects clients to the workload's endpoint.
>
> However, this simplicity comes with tradeoffs that can introduce complexities in the architecture. For instance, you may need to implement additional security measures to block OWASP attack types, a capability provided by Web Application Firewall (WAF) in Front Door or Application Gateway. Another example is adding a cache, which can speed up content delivery but adds complexity of managing a data store.
>
> For recommendations on Azure Front Door, see [Azure Well-Architected Framework perspective on Azure Front Door](./azure-front-door.md).

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

#### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the [offering-specific-aspects]. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Account for potential failures**. Azure Traffic Manager is designed to be resilient. However, it can still be a single point of failure for the workload. To mitigate this risk, you can define a secondary path with an alternate service that becomes active only when Traffic Manager is unavailable. To avoid routing issues, do not use Traffic Manager and the alternate service together.
>
> - **Have a good understanding of service level agreement (SLA) coverage**. When evaluating [Traffic Manager SLAs](https://www.azure.cn/en-us/support/sla/traffic-manager/), it's important to understand the coverage provided around the published percentile. For example, your DNS lookups may fail multiple times, but it takes a full minute of continuous DNS lookup failures for that minute to be considered downtime and counted against the 99.99% SLA.
>
> - **Incorporate redundancy in workload architecture**. Traffic Manager is a viable way to implement redundancy across Azure regions, on-premises, and other clouds, as long as the service is exposed through a public IP address. A common use case is having an on-premises application with a secondary instance in the cloud. If the on-premises system fails, the cloud instance can become active, ensuring continuity.
>
> - **Support redundancy with a reliable deployment architecture**. As a load balancer, Azure Traffic Manager distributes traffic across the workload endpoints based on how you configure its routing method. This configuration is defined in a _Traffic Manager Profile_. The profile is a core component of your deployment strategy. Active-active or active-passive model with a warm spare can be implemented with the appropriate profile configuration.
>
>   Each profile specifies a single traffic-routing method. There are scenarios that require more sophisticated traffic routing. You can combine Traffic Manager profiles to get the benefits of more than one traffic-routing method. 
>
>   For more information, see [Traffic Manager routing methods](/azure/traffic-manager/traffic-manager-routing-methods).
>
> - **Evaluate the caching duration of DNS responses**. The Time-to-Live (TTL) setting for DNS lookups in Azure Traffic Manager determines how long DNS responses are cached by downstream DNS resolvers. The default TTL may result in longer caching times than necessary, potentially causing downtime if an endpoint fails. Reducing the TTL can mitigate this issue by ensuring more frequent cache updates, but it will increase the frequency of DNS lookups.
>
> - **Avoid sending traffic to unhealthy or compromised instances**. Review the built-in health probing features in Traffic Manager. 
>
>   For HTTPS and HTTP applications, a common practice is to implement the health monitoring pattern, where you have a custom page within your application. Based on certain checks, the page returns an appropriate HTTPS status code. Ensure your health checks go beyond just checking the availability of the endpoint. Instead the health checks should include all dependencies. 
>
>   For other applications, Traffic Manager will use TCP as the monitoring protocol to determine availability of the endpoint.
>
>   For details, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring) and [Understanding Traffic Manager probes](/azure/traffic-manager/traffic-manager-troubleshooting-degraded#understanding-traffic-manager-probes).
>
> - **Determine your outage tolerance**. If a backend becomes unavailable, some time can pass before Traffic Manager recognizes the failure and stops directing traffic to the unavailable endpoint. There will be a period when client requests can't be served. Use this tolerance to configure probe settings, which will determine how quickly you want to start your business continuity operations. 
>
> - **Include the endpoints as part of your resiliency testing**. Simulate unavailability of endpoints to see the behavior of Traffic Manager handling failures. Suppose your workload uses a load balancer like Application Gateway in a private VNet. You can use Network Security Group (NSG) rules in Azure Chaos Studio to simulate failures of the endpoint. You can block access to the subnet where the Application Gateway resource resides.

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [**Deploy multiple endpoints**](/azure/traffic-manager/traffic-manager-endpoint-types) in the Traffic Manager profiles and make sure they're enabled. Otherwise it's not probed and or included in the rotation.<br><br>Place these endpoints in different regions. | Having redundant instances ensures availability if one endpoint fails.|
| Evaluate the various [**traffic routing methods**](/azure/traffic-manager/traffic-manager-routing-methods) and configure one or a combination of methods to align with your deployment strategy. Traffic Manager applies the chosen method to each DNS query, determining which endpoint is returned in the DNS response. <br> - [Weighted method](/azure/traffic-manager/traffic-manager-routing-methods#weighted-traffic-routing-method). Distributes traffic based on the configured weight coefficient, supports active-active models. <br> - [Priority-based method](/azure/traffic-manager/traffic-manager-routing-methods#priority-traffic-routing-method). Configures the primary region to receive all traffic and send traffic to the secondary region as a backup supports active-passive models. <br>- [Geographic method](/azure/traffic-manager/traffic-manager-routing-methods#geographic-traffic-routing-method). Matching is based on the geographic location that the DNS query originates from. Add at least one endpoint configured with [`All (World)`](/azure/advisor/advisor-reference-reliability-recommendations#add-an-endpoint-configured-to-all-world).|By choosing the appropriate routing method, you can ensure that traffic is distributed efficiently across your endpoints. <br><br> You can support your deployment model goals (active-active/active-passive) ensuring secondary regions can handle traffic or act as backup. <br><br> Geographical routing helps direct users to the nearest endpoint based on their location, while avoiding traffic black holes.|
|Set the [**DNS Time to Live (TTL) interval**](/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users) duration to a low value, less than 60 seconds is recommended. Adjust both, the health probe timing, and the DNS record TTL to optimize performance. | Reducing the TTL duration can ensure more frequent updates of downstream DNS resolvers cache and quicker failover, minimizing downtime and improving the overall  responsiveness of your application. |
|Set up health probes to [**monitor the endpoint**](/azure/traffic-manager/traffic-manager-monitoring). <br><br>Don't enable `Alwayserve`, which disables endpoint monitoring and requests are sent to the endpoint, regardless of its health state. <br><br>- `Probing interval`. Consider the tradeoff between how fast you can detect failure and the number of requests going to the endpoint. Number of requests can be substantial, given Traffic Manager is a global service and will ping simultaneously from various locations. <br>- `Probe timeout`. Consider how long to wait before declaring the endpoint unhealthy. Factor in the false positives in the number of failures.|Health probes make sure that only health instances serve user requests. They also can help  determine if failures are nontransient and how quickly the failover operations should take place. 

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Traffic Manager.

#### Design checklist

> [!div class="checklist"]
>
> - **Review security baselines**. To enhance the security posture, review the [security baseline for Traffic Manager](/security/benchmark/azure/baselines/traffic-manager-security-baseline).
>
> - **Prevent unauthorized modification of traffic routing**. Traffic Manager profiles hold the configuration settings for routing traffic and should be treated as critical workload resources. Only authorized identities should have access to these profiles. Implement [role-based access control (RBAC)](/azure/role-based-access-control/overview) on the control plane to restrict tasks like creating, deleting, or modifying resources, such as enabling or disabling endpoints. Unauthorized access can result in configuration changes, potentially rerouting traffic to malicious implementations.
>
> - **Protect applications from threats at the network edge**. Traffic Manager doesn't provide built-in security features like a Web Application Firewall (WAF). To secure HTTP applications, you should implement traffic inspection at the endpoint level. 
>
>   A typical architecture might include Traffic Manager with multiple endpoints, each protected by an Application Gateway that handles TLS termination and other security functions. For a reference architecture that demonstrates that pattern, see [Multi-region load balancing with Traffic Manager, Azure Firewall, and Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway).
>
> - **Harden DNS entries**. Traffic Manager can be susceptible to DNS spoofing and cache poisoning attacks, which can redirect traffic to malicious sites, causing security issues. Subdomain takeover, where a DNS record points to a deprovisioned Azure resource, is a common threat. This can be prevented by using Azure DNS alias records and coupling the lifecycle of a DNS record with an Azure resource. 
>
>   For more information, see [Prevent dangling DNS entries](/azure/security/fundamentals/subdomain-takeover#prevent-dangling-dns-entries).


#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
|[**Add application gateways to the workload endpoints**](/azure/traffic-manager/traffic-manager-load-balancing-azure#step-3-add-application-gateways-to-the-traffic-manager-endpoints). <br><br>Add security inspection with firewall for HTTP applications by adding application gateways to the workload endpoints.| You're able to  inspect inbound HTTP traffic and protect the application from common attacks.|
[**Create an alias record**](/azure/dns/tutorial-alias-tm) in Azure DNS for your workload's apex domain name to reference an Azure Traffic Manager profile to prevent dangling records if the workload is decommissioned. | Alias records prevent dangling references by tightly coupling the lifecycle of a DNS record with an Azure resource. If the Traffic Manager profile is deleted, the DNS alias record becomes an empty record set. It no longer references the deleted resource.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Traffic Manager and its environment.

#### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Evaluate the cost of features**. Traffic View is a paid dashboard feature that shows where your clients are connecting from and the associated latency. This information helps optimize performance and refine your design, contributing to operational excellence and system efficiency. However, it incurs additional costs. For more information, see [Traffic View billing](/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work).
>
>   Also consider the cost associated with health probes. Traffic Manager pings your defined endpoints from various locations to check their availability. You can choose between slow pings and fast pings. Fast pings detect failures quicker but incur higher costs. There's also added load on the workload given the higher frequency of health checks. 
>
> - **Evaluate the cost of your routing strategy**. For example, if majority of clients access your endpoint from a high-latency region, you could create another endpoint closer to those users and adjust the routing method in Traffic Manager. This reduces latency, allowing you to process more requests with less capacity, leading to cost savings.
  
#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
|Use the [**pricing calculator**](https://azure.microsoft.com/pricing/details/traffic-manager/) to estimate costs of Traffic Manager features.| You'll be able to have a more accurate cost model and set governance around resources, if necessary.|
|Enable [**Traffic View**](/azure/traffic-manager/traffic-manager-traffic-view-overview) during your optimization efforts. |The feature can help you getting better understanding of the usage patterns. This data is insightful for performance tuning to help meet your workload targets. <br><br> By enabling features at the right time and using the right tier, you'll avoid underutilizing them.
|Choose between fast or slow pings tier of [**Health probes**](/azure/traffic-manager/traffic-manager-monitoring) based on your recovery metrics. <br><br>Fast pings detect failures quicker but are more expensive, while slow pings take longer but cost less. <br><br>Disabling pings isn't recommended.|You'll be able to optimize on cost by opting to lower the frequency of pings. Also, reduce the load on the workload endpoints.| 

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

#### Design checklist

> [!div class="checklist"]
>
> - **Collect and analyze operational data as part of your workload monitoring**. Capture relevant Azure Traffic Manager logs and metrics. This data helps you troubleshoot, understand traffic behaviors, and fine-tune routing logic. 
>
> - **View traffic data for the profiles**. This data helps pinpoint areas for improvement, like expanding your Azure presence in high-latency regions. It also highlights traffic patterns across different regions, guiding decisions on where to increase or decrease investment.
>
> - **Avoid automatic failback operations**. When performing a failback, avoid immediately switching back to the original endpoint through automatic failback in the primary after it becomes available again. Instead, disable the original endpoint and continue using the secondary endpoint until you're ready. This approach allows time for stabilization. Immediate failback can cause additional load and delays. 
>
>   For more information, see this [reference architecture](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server).
>
> - **Test the configuration settings**. Misconfigurations can affect all aspects of the workload, especially reliability. Testing the configurations with multiple clients at various locations. For more information, see [Verify Traffic Manager settings](/azure/traffic-manager/traffic-manager-testing-settings).


#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
|[**Enable diagnostic logs**](/azure/traffic-manager/traffic-manager-diagnostic-logs) for a Traffic Manager profile. <br><br>These logs can be replayed using [**tools**](/azure/traffic-manager/traffic-manager-troubleshooting-degraded#troubleshooting) to analyze the health check data.| These logs offer insights into the behavior of the Traffic Manager profile, such as identifying the reasons behind individual probe timeouts against an endpoint.|
|Enable [**Traffic View**](/azure/traffic-manager/traffic-manager-traffic-view-overview) Get insights into where your clients are connecting from and the associated latency.|This information helps optimize performance and cost by allowing you to refine your design.|
|Take advantage of the [**Heat Map REST API**](/rest/api/trafficmanager/heat-map), which gets data on client locations and latency.|This approach offers more flexibility, such as setting a time period. The response can be used in custom tooling or dashboard. It also makes it easier to integrate with external tools.|
|[**Disable the endpoints for operational activities**](/azure/traffic-manager/traffic-manager-endpoint-types#enabling-and-disabling-endpoints) such as, disabling failback after a failover, temporarily disabling for maintenance or testing purposes.|Disabling the endpoint from the load balancer is beneficial for operational tasks because you can't stop live traffic. This approach is useful to avoid automatic failback. <br><br>During maintenance disabling the endpoint will stop instances from getting traffic.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and
optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

#### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Traffic Manager.

> [!div class="checklist"]
>
> - **Measure performance impact of your configuration**. Traffic Manager doesn't interfere with the direct connection between the client and your endpoint. The main performance impact is the initial DNS lookup. The Time-to-Live (TTL) setting affects how often this lookup occurs. A lower TTL means more frequent DNS resolutions, which can slightly impact performance. If TTL is set to zero, a DNS lookup is required for every request, adding a small delay before accessing the endpoint.
>
>   Because Traffic Manager operates at the DNS level, it doesn't support session affinity. Users may be directed to different endpoints from one call to the next. If you need this capability, you'll need to persist state to a separate data store.
>
>   For more information, see [Performance considerations for Traffic Manager](/azure/traffic-manager/traffic-manager-performance-considerations).
>
> - **Optimize performance by adjusting traffic routing behavior**. A single profile can have multiple endpoints, but only one routing method can be applied at a time. 
>
>   For more complex scenarios, consider creating a hierarchy of profiles to combine different routing methods, such as prioritizing regions and then using performance-based routing within regions.

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
|Use the [**Performance routing method**](/azure/traffic-manager/traffic-manager-routing-methods#performance-traffic-routing-method) when you have endpoints in different geographic locations.|This method prioritizes traffic to the endpoint with the lowest latency, ensuring faster service for users.|
|**Use specialized tools** to optimize performance: <br><br> - Measure the performance of your [DNS lookups](/azure/traffic-manager/traffic-manager-performance-considerations#sample-tools-to-measure-dns-performance). <br>- Analyze the performance of traffic by using [**Traffic View**](/azure/traffic-manager/traffic-manager-traffic-view-overview) or the [**Heat Map REST API**](/rest/api/trafficmanager/heat-map). |DNS latency measurement tools give you performance data to do a full DNS lookup. You can use this data to set the TTL duration and optimize performance. |
|Combine traffic methods with [**nested profiles**](/azure/traffic-manager/traffic-manager-nested-profiles) for optimal performance determined by the workload requirements.| By selecting the best routing method, you can ensure that traffic is directed to the most responsive and closest endpoints, improving application performance and user experience. |
|[**Use Real User Measurements**](/azure/traffic-manager/traffic-manager-rum-overview) to view network latency measurements to Azure regions. <br><br> This feature is available at no additional cost.| You'll be able to make data-driven routing decisions to direct queries to the Azure region that provides the lowest latency.  |
|Set [**DNS Time to Live (TTL) interval**](/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)  to a higher value.| The clients can cache the  results for longer, reducing the need to resolve DNS each time.

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Traffic Manager and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- resource logs are enabled to track activities
- logs for the Traffic Manager profiles are sent to Event Hubs.

For comprehensive governance, review the [Azure Policy built-in definitions for Azure networking services](/azure/governance/policy/samples/built-in-policies#network).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your web application instances.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Reliability and Performance Efficiency**

- **Time-to-Live (TTL) setting for DNS lookups**. The default TTL setting may result in longer caching times, which can cause downtime if an endpoint fails, as the application will continue to attempt to connect to the failed endpoint for the duration of the TTL.

    Reducing the TTL can mitigate this issue by ensuring more frequent updates and quicker failover, but it will increase the frequency of DNS lookups, potentially impacting performance and increasing load on DNS servers.

:::image type="icon" source="../_images/trade-off.svg"::: **Reliability and Cost Optimization**

- **Health probes**. Traffic Manager uses health probes to ping your endpoints from various locations to check their availability. You can opt for slow pings or fast pings. Fast pings detect failures quicker but comes at an additional cost. Slow pings take longer to detect failures but are less costly. You need to balance the speed of failure detection and recovery with the associated costs.


## Next step 

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

- Use the following reference architectures as examples of how you can apply this article's guidance to a workload:
   - [Multi-region load balancing with Traffic Manager, Azure Firewall, and Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)

- Build implementation expertise by using the following product documentation:
   - [Azure Traffic Manager](/azure/traffic-manager/)


