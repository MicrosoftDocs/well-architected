---
title: Architecture Best Practices for Azure Load Balancer
description: Learn about reliability, security, cost optimization, operational excellence, and performance efficiency best practices for Azure Load Balancer.
author: PageWriter-MSFT
ms.author: prwilk
ms.subservice: waf-service-guide
ms.topic: concept-article
ms.date: 02/04/2025
---

# Architecture best practices for Azure Load Balancer

The load balancing process distributes network traffic to a group of two or more back-end servers. Azure Load Balancer is an Azure-native service that does Layer-4 load balancing for User Datagram Protocol (UDP) and Transmission Control Protocol (TCP). Load Balancer helps provide low latency and high availability for regional and global deployments.

This article assumes that as an architect, you've reviewed the [load-balancing options](/azure/architecture/guide/technology-choices/load-balancing-overview) in Azure and chose Load Balancer for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- Load Balancer

This guidance focuses the Standard Load Balancer SKU. Basic Load Balancer and Gateway Load Balancer SKUs are out of scope for this article. 

:::image type="content" source="./_images/azure-load-balancer/load-balancer.png" alt-text="Diagram that shows a load balancer directing traffic." lightbox="./_images/azure-load-balancer/load-balancer.png" border="false":::

> [!NOTE]
>
> For HTTP applications, consider Azure Application Gateway or Azure Front Door instead of Load Balancer. These alternatives manage load balancing and also provide features like Web Application Firewall (WAF) and Transport Layer Security (TLS) termination.
>
> For more information, see:
>
> - [Well-Architected Framework perspective on Azure Front Door](./azure-front-door.md)
> - [Well-Architected Framework perspective on Azure Application Gateway](./azure-application-gateway.md)

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the tiers and features of virtual machines (VMs). Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Understand the impact of Microsoft-backed guarantees.** In addition to other components in your architecture, factor service-level agreements (SLA) into the reliability target of your workload. Keep in mind the following important points:
>   
>    - If the load-balanced endpoint can't connect to all healthy back-end servers for an entire minute, that minute is considered unavailable. However, if at least one request succeeds within the same minute, even if others fail, that minute isn't considered downtime.
> 
>   - Downtime doesn't include minutes caused by Source Network Address Translation (SNAT) port exhaustion. Ensure that you configure your workload to handle the expected number of connections and open ports accordingly.
> - **Support zone redundancy in your workload architecture.** We recommend the Standard Load Balancer SKU. It has reliability features, such as availability zone support, traffic distribution across multiple regions, and the ability to handle more instances in the back-end pool. These feature help withstand failures at zonal, regional, and individual VM instance levels. Be aware of limitations, such as the maximum back-end pool size.
>
>   >[!NOTE]
>   >In Load Balancer, you manage the number of load-balanced VMs but not the number of Load Balancer instances. You can configure a Load Balancer instance to be zone redundant, or pin it to a zone if the workload needs to collocate VMs in a single zone. The front-end IP address's zonal or multizone configuration dictates the load-balancing redundancy.
>
> - **Support regional redundancy in your workload architecture.** You can configure Load Balancer to be a global load balancer. In this setup, Load Balancer has a static anycast public IP address that broadcasts to multiple regions. When clients request this IP address, their requests go to the closest server instance. Load Balancer connects to regional load balancers to distribute traffic efficiently.
>
> - **Evaluate changes in your networking stack to support reliable scaling.** Consider scaling out the back-end pool by using autoscale rules. Be aware of potential SNAT port exhaustion for outbound traffic. To address this problem, use Azure NAT Gateway for easier configuration, but understand that it lacks availability zone redundancy. Alternatively, use Load Balancer for added zone redundancy. For more information, see [Outbound connections](/azure/load-balancer/load-balancer-outbound-connections). 
>
> - **Mitigate potential failures.** Do failure mode analysis and identify mitigations. The following table shows types of failures and how to mitigate them.
>
>   |Failure|Mitigation|
>   |---|---|
>   |Traffic is routed to unhealthy application instances.|Monitor workload instance health. Implement HTTP health probes that include checks for workload dependencies.|
>   |Traffic is routed to a region that has an outage.|Deploy extra instances in another region. Add a global load balancer to redirect traffic to the new region.|
>   |The workload's user base was expanded to support users in a new region, and they have high latency. The application now experiences a high number of timeouts and failures.|Deploy extra instances in a new region, and add them in the service configuration. As a global load balancer, Azure Load Balancer routes traffic closer to the users.|
>
> - **Route traffic to healthy instances.** You can use HTTP or TCP for health probes. To provide richer status responses, consider creating an HTTP endpoint for health checks, even for non-HTTP apps. This approach is especially useful for checking dependencies and databases. Without HTTP probes, the load balancer relies on TCP connections, which might not accurately reflect VM health. 
>   
>   You can configure the health probe on Load Balancer. For more information, see [Design guidance on health probes](/azure/load-balancer/load-balancer-custom-probe-overview#design-guidance).

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
|Select the Standard Load Balancer SKU. <br> For more information, see [SKU comparison](/azure/load-balancer/skus).|This SKU supports reliability features, such as availability zones and multiregion load balancing. |
| Configure [rules](/azure/load-balancer/components#load-balancer-rules) to map the front-end IP address to the back-end servers' IP addresses to enable load balancing. <br><br>The back-end address pool should have at least two back-end endpoints to load balance for redundancy.|Rules are the core of the load balancing algorithm. Without this configuration, the distribution modes are disabled.|
| Configure [health probes](/azure/load-balancer/load-balancer-custom-probe-overview#probe-configuration). <br><br> - Set the probing interval and threshold values. Consider the tradeoff between how fast you can detect failures and the number of requests to the endpoint. <br>- Evaluate whether you want to send traffic to the instances when all instances have an unhealthy status. You might use this configuration to implement a graceful degradation experience. For more information, see [AllProbedUp](/rest/api/load-balancer/load-balancer-probes/list#probenohealthybackendsbehavior). | Only healthy back-end pool instances receive new connections. This configuration helps maintain high availability and reliability because it routes traffic away from unhealthy instances.|
| Configure private and public IP addresses to be [zone redundant](/azure/reliability/reliability-load-balancer#availability-zone-support). The IP address determines the zone redundancy of Load Balancer. | Zone redundancy helps the workload withstand zonal failures. When one zone fails, services can fail over to one of the remaining zones. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Load Balancer.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines.** To enhance the security posture of your application that's load balanced by Azure Load Balancer, review the [security baseline for Load Balancer](/security/benchmark/azure/baselines/azure-load-balancer-security-baseline).
>
> - **Protect the back-end servers.** Deploy resources in a virtual network that doesn't have direct internet exposure. Front the virtual network with a load balancer. Ideally, the load balancer should have firewall capabilities. For HTTP applications, consider Application Gateway or Azure Front Door. For non-HTTP applications, consider Load Balancer with a private IP address (internal load balancer), and route traffic through Azure Firewall for added security. For more information, see [Internal load balancer](/azure/firewall/integrate-lb#internal-load-balancer).  
>
>   You can also use Load Balancer as a reverse proxy. In that case, the load balancer has a public IP address with SNAT, which exposes resources while masking their IP addresses.
>
>   >[!NOTE]
>   >To filter traffic to back-end servers, use network security groups (NSGs) on the subnets that contain the front end and back end. Don't apply NSGs directly to the Load Balancer service. When NSGs enforce rules, they consider the source ports, destination ports, and address ranges of the originating and destination computers, not the load balancer.
>
> - **Design for private connectivity.** Load Balancer works with Azure Private Link. If you spread application resources across virtual networks, you can connect resources in different virtual networks. Use virtual network peering or place Private Link in front of the internal load balancer. The Private Link option provides more secure access without needing a public IP address. It also restricts access from nonpeered networks.
>
>   You can authorize private links via [role-based access control](/azure/private-link/rbac-permissions) to restrict access to only the identities that need it.
>
> - **Protect your application from threats at the network edge.** For designs that use Load Balancer as the point of entry, implement traffic inspection at the endpoint level. This design doesn't have built-in security features like a WAF, so you must add extra measures to help secure HTTP applications. For more information, see [Public load balancer](/azure/firewall/integrate-lb#public-load-balancer). Also ensure that you protect the load balancer endpoints from distributed denial-of-service (DDoS) attacks. 
> 
> - **Encrypt network traffic.** Load Balancer works at Layer 4 and fully supports load balancing TCP and UDP traffic. Load Balancer doesn't support Secure Sockets Layer (SSL) and TLS termination. For HTTPS load balancing at the application layer, use Application Gateway.

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [Configure the front-end IP address](/azure/load-balancer/components#frontend-ip-configurations) to a private IP address in a virtual network. | This approach helps ensure that the front-end IP address and virtual network remain isolated from direct internet exposure. The internal load balancer can't accept incoming traffic from the internet, which reduces potential attack vectors. |
|[Protect the public load balancer with Azure DDoS Protection.](/azure/load-balancer/tutorial-protect-load-balancer-ddos)| A DDoS Protection plan provides advanced protection, including detection capabilities that monitor the endpoint for threats and signs of abuse.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Load Balancer and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Factor load balancing expenses into your cost model.** Consider primary factors, such as the amount of data that Load Balancer processes and the number of inbound and outbound load balancing rules. For a precise cost estimation, use traffic logs to gauge your inbound and outbound traffic needs. 
> 
> - **Set controls on spending.** Log and analyze Load Balancer costs. To manage costs effectively, use [Microsoft Cost Management](/azure/cost-management-billing/costs/overview-cost-management) to [create budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) and configure alerts. Costs can accumulate based on the amount of logged data and the storage duration, which affects bandwidth and storage expenses.
>
> - **Remove unused resources.** Identify and remove unused load balancer instances. Analyze logs to evaluate usage. Delete load balancer instances that aren't associated with back-end VMs. Examine traffic logs to find underused resources.
>
> - **Optimize flow costs.** Use efficient protocols and data compression to reduce the load on the traffic flow and minimize costs. 
>
>   To optimize costs, you can reduce the number of rules. Instead of having rules that use individual IP addresses and ports for each endpoint, define a rule for a range of ports in the front end that connects to a back-end pool.
>
>   Implement optimization in the back-end flows. For example, multiple database queries that a load balancer intercepts can increase costs per query. To prevent this extra cost, consider implementing a stored procedure to consolidate the sequence of queries. 
>
> - **Evaluate the cost of operations.** Consider resource expenses and operational costs, like maintenance, scaling, and compliance. Load balancer rules can significantly affect costs. Reduce the number of rules to optimize financial and management costs.

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
|Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/details/load-balancer/) to estimate costs.| You can convert anticipated traffic usage into cost estimates, which makes it easier to plan and budget.|
|Evaluate the number of rules and reduce them, if possible. <br><br>Evaluate whether you can use one rule to summarize a range of ports instead of defining several rules for individual IP addresses. <br>For example, you can use [inbound NAT rules](/azure/load-balancer/inbound-nat-rules) to map IP addresses and ports to a back-end pool rather than to individual VMs. | Consolidated rules optimize costs and simplify operations. <br><br>When you scale up or down, you can add or remove IP addresses from the back-end pool without changing any rules.|


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Load Balancer.

> [!div class="checklist"]
>
> - **Use infrastructure as code.** Deploy and configure Load Balancer along with other networking components, like virtual networks, network peerings, private endpoints, and NSGs. Familiarize yourself with the [Microsoft.Network loadBalancers resource type](/azure/templates/microsoft.network/loadbalancers?pivots=deployment-language-bicep).
>
> - **Use layered deployment for hub-and-spoke architectures.** Deploy the hub first because it changes less frequently than the workload that's deployed in the spoke network. Deploy the load balancer with the workload. If you reuse a single load balancer across multiple workloads, consider placing it in the hub.
>
> - **Implement a comprehensive networking monitoring system.** Impelement diagnostic capabilities, like multidimensional metrics for real-time insights and alerts, resource logs based on the health event schema, and the Azure Monitor Insights dashboard for comprehensive load balancer monitoring.

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use [multidimensional metrics](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics). <br><br>To minimize excessive alerts, set the aggregation type to `Average`, and use a five-minute data window that has a 95% threshold. For more information, see [Configure alerts for multidimensional metrics](/azure/load-balancer/load-balancer-standard-diagnostics#configure-alerts-for-multi-dimensional-metrics). Review the examples for inbound and outbound availability.| Comprehensive real-time insights and alert configuration provide enhanced detection of problems and enable prompt responses. |
| Capture resource logs. Load Balancer entries depend on the [health event schema](/azure/azure-monitor/reference/tables/albhealthevent). | Logs provide detailed records of events so that you can quickly identify and resolve problems.|
| Use the built-in [Azure Monitor Insights dashboard for Load Balancer](/azure/load-balancer/load-balancer-insights). | Visualization facilitates well-informed design choices and helps you quickly identify, diagnose, and fix problems.|
| During maintenance operations, set the [Admin state](/azure/load-balancer/admin-state-overview) to `Down` to take a back-end instance out of rotation without disrupting existing connections. This configuration helps ensure that no new connections are forwarded to the back-end instance, while existing connections are gracefully terminated.| This *Admin state* configuration helps reduce overhead and complexity when you take a VM out of the load balancing rotation for regular maintenance or patching. <br><br> As an alternative option to take a back-end instance out of rotation, you can apply NSGs to block traffic from the Load Balancer health probe or the client's IP addresses and ports. This option increases complexity.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline that's based on key performance indicators for Load Balancer.

> [!div class="checklist"]
>
> - **Determine the network performance targets.** The load balancer has no limit on the traffic that it can support. But when you define performance targets and plan for capacity, you should test network performance.
>
>   Use stress testing to understand the workload's bandwidth requirements. Include the load balancer in those tests. If a single virtual machine scale set with multiple VMs isn't sufficient, you might add another scale set by using the same load balancer. If the VMs don't receive requests quickly enough, you might need to adjust the networking components, such as adding more load balancers. But instead of changing the load balancer, consider making design changes and optimizing your workload to handle the load better.
>   
> - **Understand the limits when you design your scaling strategy.** To meet performance requirements and scale your workload, add or remove VMs from the back-end pool. A single back-end pool in Standard Load Balancer can handle up to 5,000 VMs.
>
>   Load Balancer doesn't apply throughput limits. But throughput limits for VMs and virtual networks still apply. For more information, see [VM network bandwidth](/azure/virtual-network/virtual-machine-network-throughput).
>  
> - **Serve requests quickly.** Standard Load Balancer has a tier that routes traffic to back-end endpoints based on their geographic proximity to the user.
> 
>   Load Balancer also supports load distribution based on session persistence. When you enable this feature, requests from the same client are consistently directed to the same back-end server that handled their previous sessions.
>
> - **Collect data to analyze performance.** Load Balancer [multidimensional metrics](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) can analyze the service's performance. Configure alerts to detect performance changes. Use tools like the [Azure Monitor Insights dashboard](/azure/load-balancer/load-balancer-insights) to visualize the status of Load Balancer. Ensure that the resource health feature monitors the health status, and stay informed about performance problems and outages.
>
> - **Optimize network traffic.** Don't process the same data multiple times in separate steps. Instead, perform all necessary calculations in a batch, and then persist the data. This approach reduces latency and minimizes network traffic, which improves overall performance.

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Select the [global tier in Standard Load Balancer](/azure/load-balancer/cross-region-overview) if you have global users.| This tier's geo-proximity distribution mode serves user requests from endpoints in the nearest region, which improves performance. |
|Evaluate whether you should enable [session persistence](/azure/load-balancer/distribution-mode-concepts#session-persistence) when you want requests from the same user to direct to the same back-end server. <br><br> From a reliability perspective, we don't recommend this approach. If you use this option, the application should gracefully recover without disrupting user sessions. <br><br> There's also a load balancing tradeoff because it restricts the flexibility of distributing traffic across multiple back ends evenly.| Session persistence can optimize performance and maintain continuity for user sessions, especially when applications rely on maintaining state information locally. But there are tradeoffs.
|During scale-out, send a [probe-down signal](/azure/load-balancer/load-balancer-custom-probe-overview#probe-down-behavior) until the application is fully initialized and ready to handle requests. <br><br>During scale-in, send a probe-down signal for new connections on the endpoint that's being scaled back. Pending requests on existing connections continue to be processed.|Health probes can help optimize scaling operations. They help ensure that during scale-out, the application can handle the incoming load. Before a scale-in operation, they allow for a smooth reduction in instances without disrupting ongoing operations.|

## Azure policies

Azure provides an extensive set of built-in policies related to Load Balancer and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Load balancers, excluding Basic SKU load balancers, have resiliency features enabled for public IP addresses in their front end. 
- Resource logs are enabled to track activities and events that occur on your resources and provide visibility and insights into changes. 

For comprehensive governance, review the [Azure Policy built-in definitions for Load Balancer](/azure/governance/policy/samples/built-in-policies) and other policies that might impact the security of traffic distribution.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Azure Virtual Machines baseline architecture](/azure/architecture/virtual-machines/baseline).

## Related resources

- [Virtual Machines baseline architecture](/azure/architecture/virtual-machines/baseline)
- [Load Balancer documentation](/azure/load-balancer)
