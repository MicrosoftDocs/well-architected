---
title: Recommendations for designing for redundancy
description: Learn how to design for redundancy in your workload to meet your reliability targets and to keep problems contained to a single resource.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing for redundancy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:05**| **Add redundancy at different levels, especially for critical flows**, to help meet your reliability targets. Consider redundant infrastructure components such as compute and network, and multiple instances of your solution.  |
|---|---|

This guide describes the recommendations for adding redundancy throughout critical flows at different workload layers, which optimizes resiliency. Meet the requirements of your defined reliability targets by applying the proper levels of redundancy to your compute, data, networking, and other infrastructure tiers. Apply this redundancy to give your workload a strong, reliable foundation to build on. When you build your workload without infrastructure redundancy, there's a high risk of extended downtime due to [potential failures](failure-mode-analysis.md).

**Definitions**

| Term | Definition |
|---------|---------|
| Redundancy | The implementation of multiple identical instances of a workload component. |
| Region | This guide uses the term region to refer to [Azure datacenter locations](azure/reliability/regions-list). |
| Polyglot persistence | The concept of using different storage technologies by the same application or solution to take advantage of the best capabilities of each component. |
| Data consistency | The measure of how in sync or out of sync a given dataset is across multiple stores. |
| Partitioning | The process of physically dividing data into separate data stores. |
| Shard | A horizontal database partitioning strategy that supports multiple storage instances with a common schema. Data isn't replicated in all instances. |

## Key design strategies

In the context of reliability, use redundancy to contain problems that affect a single resource and ensure that those problems don't affect the reliability of the entire system. Use the information that you identified about your critical flows and reliability targets to make informed decisions that are required for each flow's redundancy.

For example, you might have multiple web server nodes running at once. The criticality of the flow that they support might require that all of them have replicas that are ready to accept traffic if there's a problem that affects the entire pool, for example a full datacenter outage. Alternatively, because large-scale problems are rare and it's costly to deploy an entire set of replicas, you might deploy a limited number of replicas so the flow operates in a degraded state until you resolve the problem.

When you design for redundancy in the context of performance efficiency, distribute the load across multiple redundant nodes to ensure that each node performs optimally. In the context of reliability, build in spare capacity to absorb failures or malfunctions that affect one or more nodes. Ensure that the spare capacity can absorb failures for the entire time that's needed to recover the affected nodes. With this distinction in mind, both strategies need to work together. If you spread traffic across two nodes for performance and they both run at 60 percent utilization and one node fails, your remaining node is at risk of becoming overwhelmed because it can't operate at 120 percent. Spread the load out with another node to ensure that your performance and reliability targets are upheld.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoffs**:
> - More workload redundancy equates to more costs. Carefully consider adding redundancy and regularly review your architecture to ensure that you're managing costs, especially when you use overprovisioning. When you use overprovisioning as a resiliency strategy, balance it with a well-defined [scaling strategy](scaling.md) to minimize cost inefficiencies.

> - There can be performance tradeoffs when you build in a high degree of redundancy. For example, resources that spread across availability zones or locations can affect performance because you have to send traffic over high-latency connections between redundant resources, like web servers or database instances.

> - Different flows within the same workload might have different reliability requirements. Flow-specific redundancy designs can potentially introduce complexity into the overall design.

> - Using a low-latency redundancy design means that you accept the risk of losing those components in the event of a large-scale event, like a geographic disaster, that takes all instances offline. Having a geo-distant disaster recovery environment helps mitigate this risk, while increasing costs.

### Simplify your design and reduce your operational burden with managed infrastructure

Take advantage of [serverless](https://azure.microsoft.com/en-us/solutions/serverless), software as a service (SaaS), and platforma as a service (PaaS) services to easily add redundancy to your workload without needing to manage data replication or failover operations. Serverless and SaaS services transparently scale as demand changes helping you maintain your reliability targets without creating custom scaling strategies. These services also transparently failover when localized issues occur. PaaS services can have different degrees of support for automatic scaling and failover. Some may require you to configure policies and perform manual actions, while others have built-in capabilities. Review your cloud provider's options for workload components that fit your use case and determine which of those are the best match for you when balancing reliability, performance, and costs.

### Deploy multiple instances of your workload

The starting point for all redundant design strategies is to deploy a secondary instance of your workload alongside the primary instance. As a first step, determine how many instances of your workload satisfies your reliability targets and whether you need full instances of your workload, or if you only need to deploy multiple instances of certain components. Your workload design should influence these decisions. If you use managed compute services, you might only need to deploy a secondary database and configure replication to achieve redundancy. Likewise, your automation maturity should also influence this approach. When you have the ability to efficiently deploy your full application at any time through automation, your redundancy requirements might be significantly different than being at a state where you must deploy components manually. When deploying redundant infrastructure, consider the following recommendations.

**Compute resources**

- When using VMs, prefer services that have support for redundancy built-in, like Azure VM Scale Sets.

- Use Azure Virtual Machine Scale Sets if you need to deploy virtual machines (VMs). With Virtual Machine Scale Sets, you can automatically spread your compute evenly across availability zones.

- Keep your compute layer *clean of any state* because individual nodes that serve requests might be deleted, faulted, or replaced at any time.

- Design and test your scaling strategy to match your redundancy approach.

**Data resources**

- Determine whether synchronous or asynchronous data replication is necessary for your workload's functionality. To help you make this determination, see [Recommendations for using availability zones and regions](regions-availability-zones.md).

- Consider the growth rate of your data. For capacity planning, plan for data growth, data retention, and archiving to ensure your reliability requirements are met as the amount of data in your solution increases.  

- Distribute data geographically, as supported by your service, to minimize the effect of geographically localized failures.

- Replicate data across geographical regions to provide resilience to regional outages and catastrophic failures.

- Automate failover after a database instance failure. You can configure automated failover in multiple Azure PaaS data services. Automated failover isn't required for data stores that support multi-region writes, like Azure Cosmos DB. For more information, see [Recommendations for designing a disaster recovery strategy](disaster-recovery.md).

- Use the best [data store](/azure/architecture/guide/design-principles/use-best-data-store) for your data. Embrace polyglot persistence or solutions that use a mix of data store technologies. Data includes more than just persisted application data. It also includes application logs, events, messages, and caches.

- Consider data consistency requirements and use [eventual consistency](/azure/architecture/guide/design-principles/minimize-coordination#recommendations) when requirements allow it. When data is distributed, use appropriate coordination to enforce strong consistency guarantees. Coordination can reduce your throughput and make your systems tightly coupled, which can make them more brittle. For example, if an operation updates two databases, instead of putting it into a single transaction scope, it's better if the system can accommodate eventual consistency.

- Partition data for availability. [Database partitioning](/azure/architecture/best-practices/data-partitioning) improves scalability and it can also improve availability. If one shard goes down, the other shards are still reachable. A failure in one shard only disrupts a subset of the total transactions.

- If sharding isn't an option, you can use the [Command and Query Responsibility Segregation (CQRS) pattern](/azure/architecture/patterns/cqrs) to separate your read-write and read-only data models. Add more redundant read-only database instances to provide more resilience.  

- Understand the built-in replication and redundancy capabilities of the stateful platform services that you use. For specific redundancy capabilities of stateful data services, see [Related links](#related-links).

**Networking resources**

- Decide on a reliable and scalable network topology. Use a hub-and-spoke model or an Azure Virtual WAN model to help you organize your cloud infrastructure in logical patterns that make your redundancy design easier to build and scale.

- Select the appropriate [network service](/azure/architecture/guide/technology-choices/load-balancing-overview) to balance and redirect requests within or across regions. Use global or zone-redundant load balancing services when possible to meet your reliability targets.

- Ensure that you have allocated sufficient IP address space in your virtual networks and subnets to account for planned usage, including scale-out requirements.

- Ensure that the application can scale within the port limits of the chosen application hosting platform. If an application initiates several outbound TCP or UDP connections, it might exhaust all available ports and cause poor application performance.

- Choose SKUs and configure networking services that can meet your bandwidth and availability requirements. A VPN gateway's throughput varies based on their SKU. Support for zone redundancy is only available for some load balancer SKUs.

- Ensure that your design for handling DNS is built with a focus on resilience and supports redundant infrastructure.

### Use a redundant architecture design

Active-active and active-passive are the two fundamental approaches to designing a highly available cloud environment. Both designs can be deployed across geo-distant datacenters (known as regions in the Azure platform) or across physically independent datacenter sectors (known as availability zones in the Azure platform). Active-active environments are designed to handle production loads in every location in which you deploy your workload. Active-passive environments are designed to handle production loads only in the primary location but fail over to the secondary (passive) location when necessary. 

This section describes design options that you should consider when you evaluate each pattern and refine your architecture to meet your business requirements.

The following sections describe the design options of the two patterns.

#### Deploy in active-active for zero downtime

-   **Active-active at capacity**: Mirrored deployment stamps in two or more locations, each configured to handle production workloads for the location or locations they serve and scalable to handle loads from other locations in case of a regional outage.

    -   Networking: Use [latency](/azure/frontdoor/routing-methods#latency) or [weighted](/azure/frontdoor/routing-methods#weighted) global routing to spread traffic among locations.

    -   Data replication and consistency: Use a globally distributed data store like [Azure Cosmos DB](/azure/cosmos-db/introduction) for multi-region read and write capabilities. For relational databases, use [readable replicas](/azure/azure-sql/database/read-scale-out) with read-only connection strings.

    -   Advantage of this design: Lower operating costs than an overprovisioned design.

    -   Disadvantage of this design: Possible degradation of the user experience when scaling up to meet the demands of a full load if another location experiences an outage.

-   **Active-active overprovisioned**: Mirrored deployment stamps in two or more locations, each overprovisioned to handle production workloads for the location or locations they serve and to handle loads from other locations in case of a regional outage.

    -   Networking: Use [latency](/azure/frontdoor/routing-methods#latency) or [weighted](/azure/frontdoor/routing-methods#weighted) global routing to spread traffic among locations.

    -   Data replication and consistency: Use a globally distributed data store like [Azure Cosmos DB](/azure/cosmos-db/introduction) for multi-region read and write capabilities. For relational databases, use [readable replicas](/azure/azure-sql/database/read-scale-out) with read-only connection strings.

    -   Advantage of this design: The most resilient design possible.

    -   Disadvantage of this design: Higher operating costs than a scalable design.

-   Common advantages of both designs: High resiliency and low risk of full workload outage.

-   Common disadvantages of both designs: Higher operating costs and management burden due to various factors, including the necessity of managing the synchronization of application state and data.

#### Deploy in active-passive for disaster recovery

-   **Warm spare**: One primary location and one or more secondary locations. The secondary location is deployed with the minimum possible compute and data sizing and runs without load. This location is known as a *warm spare* location. Upon failover, the compute and data resources are scaled to handle the load from the primary location.

    -   Networking: Use [priority](/azure/frontdoor/routing-methods#priority) global routing.

    -   Data replication and consistency: Replicate your database to your passive location and use the automatic failover capabilities of platform as a service (PaaS) solutions like [Azure Cosmos DB](/azure/cosmos-db/how-to-manage-database-account#automatic-failover) and [Azure SQL Database](/azure/azure-sql/database/auto-failover-group-sql-db).

    -   Advantage of this design: Shortest recovery time among the active-passive designs.

    -   Disadvantage of this design: Highest operating cost among the active-passive designs.

-   **Cold spare**: One primary location and one or more secondary locations. The secondary location is scaled to handle full load, but all compute resources are stopped. This location is known as a *cold spare* location. You need to start the resources before failover.

    -   Networking: Use [priority](/azure/frontdoor/routing-methods#priority) global routing.

    -   Data replication and consistency: Replicate your database to your passive location and use the automatic failover capabilities of PaaS solutions like [Azure Cosmos DB](/azure/cosmos-db/how-to-manage-database-account#automatic-failover) and [Azure SQL Database](/azure/azure-sql/database/auto-failover-group-sql-db).

    -   Advantage of this design: Lower operating costs than the warm spare design.

    -   Disadvantage of this design: Longer recovery time than the warm spare design.

### Use the deployment stamp design pattern

Whether you deploy in an active-active or active-passive model, follow the [Deployment Stamps design pattern](/azure/architecture/patterns/deployment-stamp) to ensure that you deploy your workload in a repeatable, scalable way. Deployment stamps are the groupings of resources that are required to deliver your workload to a given subset of your customers. For example, the subset might be a geographic subset or a subset with all the same data privacy requirements as your workload. Think of each stamp as a *unit of scale* that you can duplicate to scale your workload horizontally or to perform blue-green deployments. Design your workload with deployment stamps to optimize your active-active or active-passive implementation for resiliency and management burden. Planning for multi-region scale out is also important to overcome potential temporary resource capacity constraints in a region.

### Use low-latency datacenter redundancy

Deploying to datacenters, or independent datacenter sectors in close proximity to one another can give you the benefits of redundancy while allowing you to deploy your high-performance workload as if all deployment stamps were deployed on colocated infrastructure. In the Azure platform, you can use [availability zones](../design-guides/regions-availability-zones.md) to spread your workload across independent infrastructure within a region. Using this approach let's you take advantage of the inherent low latency to achieve transparent network load balancing and near real-time data replication. This approach is best suited for workloads with low latency requirements due to the inherent risk of losing all active workload instances in a large scale event.

### Use geo-distant datacenter redundancy

Deploying your workload across geo-distant datacenters (known as regions in the Azure platform) increases your reliability by protecting it against the risk of a large-scale event that takes an entire datacenter offline. When designing this approach, test the latency between your deployment locations and understand how the latency will affect the performance of your application. Determine whether an active-active or active-passive approach is the best fit for your use case.

## Azure facilitation

The Azure platform helps you optimize the resiliency of your workload and add redundancy by:

- Helping you select the best Azure regions for your multi-region architecture with the [Select Azure Regions guide](/azure/cloud-adoption-framework/ready/azure-setup-guide/regions).

- Providing built-in redundancy with many PaaS and SaaS solutions, some of which are configurable.

- Allowing you to design and implement intra-region redundancy by using [availability zones](/azure/reliability/availability-zones-overview) and inter-region redundancy.

- Providing zone-redundant compute solutions, like [Azure VM Scale Sets](/azure/reliability/reliability-virtual-machine-scale-sets)

- Offering replica-aware load balancing services like [Azure Application Gateway](/azure/application-gateway/), [Azure Front Door](/azure/frontdoor/), and [Azure Load Balancer](/azure/load-balancer/).

- Offering easily implemented geo-replication solutions like [active geo replication](/azure/azure-sql/database/active-geo-replication-overview) for Azure SQL Database. Implement [global distribution](/azure/cosmos-db/distribute-data-globally) and transparent replication by using Azure Cosmos DB. Azure Cosmos DB offers two options for [handling conflicting writes](/azure/cosmos-db/conflict-resolution-policies). Choose the best option for your workload.

- Offering point-in-time restore capabilities for many PaaS data services.

- Mitigating port exhaustion via [Azure NAT Gateway](/azure/nat-gateway/nat-overview) or [Azure Firewall](/azure/firewall/overview).

### DNS-specific Azure facilitation

- For internal name resolution scenarios, use Azure DNS private zones, which have built-in zone redundancy and geo redundancy. For more information, see [Azure DNS private zone resiliency](/azure/dns/private-dns-resiliency).

- For external name resolution scenarios, use Azure DNS public zones, which have built-in zone redundancy and geo redundancy.

- The public and private Azure DNS services are global services that are resilient to regional outages because zone data is globally available.

- For hybrid name resolution scenarios between on-premises and cloud environments, use Azure DNS Private Resolver. This service supports zone redundancy if your workload is located in a region that supports availability zones. A zone-wide outage requires no action during zone recovery. The service automatically self-heals and rebalances to take advantage of the healthy zone. For more information, see [Resiliency in Azure DNS Private Resolver](/azure/dns/private-resolver-reliability).

- To eliminate a single point of failure and achieve a more resilient hybrid name resolution across regions, deploy two or more Azure DNS private resolvers across different regions. DNS failover, in a conditional forwarding scenario, is achieved by assigning a resolver as your primary DNS server. Assign the other resolver in a different region as a secondary DNS server. For more information, see [Set up DNS failover by using private resolvers](/azure/dns/tutorial-dns-private-resolver-failover).

## Example

For an example of a multi-region redundant deployment, see [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

The following diagram shows another example:

:::image type="content" source="media/redundancy/reliable-web-app-dotnet.png" alt-text="Diagram that shows the architecture of the reference implementation." border="false" lightbox="media/redundancy/reliable-web-app-dotnet.png":::

## Related links

To learn more about redundancy, see the following resources:

- [Azure regions guide](/azure/cloud-adoption-framework/ready/azure-setup-guide/regions)
- [Azure Storage redundancy](/azure/storage/common/storage-redundancy)
- [Zone-redundant storage](/azure/storage/common/storage-redundancy#zone-redundant-storage)
- [Azure SQL Database active geo-replication](/azure/azure-sql/database/active-geo-replication-overview)
- [Configure replication between two managed instances](/azure/azure-sql/managed-instance/replication-between-two-instances-configure-tutorial)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 
