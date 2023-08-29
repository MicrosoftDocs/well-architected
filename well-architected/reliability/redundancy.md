---
title: Recommendations for designing for redundancy
description: Learn how to design for redundancy.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing for redundancy

**Applies to RE: 04**

This guide describes the best practices for optimizing your workload's resiliency by adding redundancy throughout the critical flows at different workload layers. Based on the quantified reliability targets that you have defined, you can apply the proper levels of redundancy to your compute, data, networking and other infrastructure tiers to meet those requirements. Applying this redundancy gives you a strong foundation to build upon in your efforts to make your workloads reliable. Building your workload without infrastructure redundancy puts the workload at a high risk of extended downtime due to all of the types of failures discussed in the \[failure mode analysis guide\](link to RE:02).

**Definitions**


|Term  |Definition  |
|---------|---------|
|Redundancy     |Having multiple identical instances of a workload component        |
|Polygot persistence     |The concept of using different storage technologies by the same application or solution, leveraging the best capabilities of each component         |
|Data consistency     |The measure of how in or out of sync a given dataset is across multiple stores         |
|Partitioning     | The process of physically dividing data into separate data stores        |
|Shard     | A horizontal database partitioning strategy that supports multiple storage instances with a common schema, but data is not replicated in all instances        |

## Key design strategies

In the context of reliability, we use redundancy to minimize the chances of issues affecting a single resource will cause a reliability impact to the system. Using the information that you have compiled when identifying your critical flows and reliability targets, you can make informed decisions on the level of redundancy that will be required for each flow. For example, you may have multiple web server nodes running at any given time. You may decide that the criticality of the flow that they are supporting requires that all of them have replicas ready to accept traffic in the case of an issue that affects the entire pool (like a regional outage, for example). Alternatively, you may decide that because large-scale issues are rare and deploying an entire set of replicas is costly, you only need to have a limited number of replicas deployed and that flow can operate in a degraded state until the issue is resolved.

When we design for redundancy in the context of performance efficiency, we are interested in distributing load across multiple redundant nodes to ensure that each node is performing optimally. In the context of reliability, we are interested in building in spare capacity to absorb failures or malfunctions affecting one or more nodes for long enough to recover the affected nodes.With this distinction in mind, both strategies need to work together. If you have spread traffic across two nodes for performance and they are both running at 60% utilization and one node fails, your remaining node will be at risk of quickly becoming overwhelmed as it cannot operate at 120%. Therefore, it is best to spread the load out with an additional node to ensure that your performance targets are upheld along with your reliability targets.

This guide provides examples of ways to use redundancy to address your reliability requirements at the larger workload-wide level and at the level of individual infrastructure resources.

## Redundant architecture design

When designing a redundant architecture, there are two approaches to consider, depending on the criticality of the user and system flows supported by the infrastructure components: active/active and active/passive. In terms of reliability, a multi-region active/active design will help you achieve the highest level of reliability possible, but it comes with a significant cost premium when compared to an active/passive design. Refer to the high availability design guide (link to HA guide) for details on the two architecture approaches. You can also use these design approaches within a single region using availability zones. Refer to the Well-Architected Framework guide for detailed guidance on availability zones and redundancy.

## Deployment stamps and units of scale

Whether you deploy in an active/active or active/passive model, follow the [deployment stamps design pattern](https://learn.microsoft.com/azure/architecture/patterns/deployment-stamp) to ensure that your workload is deployed in a repeatable, scalable way. Deployment stamps are the groupings of resources required to serve your workload to a given subset of your customers (this can be a regional subset or a subset with all of the same data privacy requirements for example). Each stamp can be thought of as a "unit of scale" that can be duplicated to scale your workload horizontally or to perform blue-green deployments. By designing your workload in this manner, your active/active and active/passive implementation will be optimized for resiliency and management burden.

## Availability zones within Azure regions

Whether you deploy an active/active or an active/passive design, take advantage of [availability zones](https://review.learn.microsoft.com/azure/reliability/availability-zones-overview) within the active regions to fully optimize your resiliency. Many Azure regions provide multiple availability zones, which are separated groups of data centers within a region. Depending on the Azure service, you can take advantage of availability zones by deploying elements of your workload redundantly across zones, or pinning elements to specific zones. For detailed guidance on designing your solution to use availability zones, refer to the Well-Architected Framework [guide.](https://review.learn.microsoft.com/azure/well-architected/regions-availability-zones?branch=pr-en-us-771)

## Infrastructure layer specific guidance

### Compute resources

-   Choose the [right compute service](https://learn.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree) for your workload. Depending on the type of workload you are designing, there may be multiple options available to you. Choosing the right service requires researching the services available and understanding which types of workloads work best on a given compute service. For example, SAP workloads are typically best suited for IaaS compute services, whereas for a containerized application, you will need to determine the specific functionality you need to have control over to determine whether to use a Kubernetes service or a PaaS service that is more fully managed by your cloud platform.

-   If your requirements allow for it, use PaaS compute options. PaaS services are fully managed by Azure, reducing your management burden and a documented degree of redundancy is built in.

-   When deploying VMs is necessary, use VM Scale Sets (VMSS). VMSS allows you to automatically spread your compute evenly across availability zones.

-   Be sure to keep your compute layer "clean of any state" because individual nodes that serve requests might be deleted, faulted, or replaced at any time.

-   Use zone-redundant services where possible to provide higher resilience without increasing your operational burden.

-   Overprovision critical resources to mitigate failures of redundant instances so the system continues to operate after a component failure, even before auto-scaling operations are initiated. Be sure to calculate the acceptable impact of a fault when incorporating overprovisioning into your redundancy design. As with your decision-making process with redundancy in general, your reliability targets and financial trade-off decisions will determine the extent that you add spare capacity through overprovisioning. In this context, overprovisioning specifically refers to scaling out, which means adding additional instances of a given compute resource type, rather than increasing the compute capabilities of any single instance (for example, changing a VM from a lower-tier SKU to a higher SKU).

-   Unlike some PaaS services, IaaS services must be deployed either manually or through automation in each availability zone and/or region that you intend to provide your solution in. Some PaaS services have built-in capabilities to be automatically replicated across availability zones and regions.

### Data resources

-   Determine whether synchronous or asynchronous data replication will be necessary for your workload's functionality. Refer to the well architected framework [guide](https://learn.microsoft.com/azure/well-architected/reliability/regions-availability-zones) to help you make this determination.

-   Consider the growth rate of your data. Plan for data growth, data retention, and archiving is essential in capacity planning to ensure your reliability requirements continue to be met as the amount of data in your solution increases.  

-   Distribute data geographically, as supported by your service, to minimize the impact of geographically localized failures.

-   Replicate data across geographical regions to provide resilience to regional outages and catastrophic failures.

-   Automate failover after a database instance failure. Automated failover is configurable in multiple Azure PaaS data services. This is not required for data stores that support multi-region writes, like Cosmos DB. Refer to the disaster recovery guide (link to RE:08) for detailed replication and failover guidance.

-   [Use the best data store for your data.](https://learn.microsoft.com/azure/architecture/guide/design-principles/use-best-data-store) Embrace polyglot persistence, or solutions that use a mix of data store technologies. Data includes more than just persisted application data. It also includes application logs, events, messages, and caches.

-   Consider data consistency requirements, and [embrace eventual consistency](https://learn.microsoft.com/azure/architecture/guide/design-principles/minimize-coordination#recommendations) when requirements allow it. When data is distributed, it takes coordination to enforce strong consistency guarantees and coordination can reduce your throughput as well as make your systems tightly coupled, which can make them more brittle. For example, suppose an operation updates two databases. Instead of putting it into a single transaction scope, it\'s better if the system can accommodate eventual consistency. 

-   Partition data for availability. [Database partitioning](https://learn.microsoft.com/azure/architecture/best-practices/data-partitioning) is often used to improve scalability, but it can also improve availability. If one shard goes down, the other shards can still be reached. A failure in one shard will only disrupt a subset of the total transactions.

-   If sharding is not an option, you can use the [CQRS](https://learn.microsoft.com/azure/architecture/patterns/cqrs) pattern to separate your read-write and read-only data models and then add additional redundant read-only database instances to provide additional resilience.  

-   Understand the built-in replication / redundancy capabilities of the stateful platform services you use. Refer to the Related links section below for specific redundancy capabilities for stateful data services.

### Networking

-   Decide on a reliable and scalable network topology. Using a hub and spoke or a Virtual WAN model can help you organize your cloud infrastructure in logical patterns that will make your redundancy design easier to build and scale.

-   Select the [right network service](https://learn.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) to balance and redirect requests within and/or across regions. Use global or zone redundant load balancing services whenever possible to meet your reliability targets.

-   Ensure that you have allocated sufficient IP address space in your VNets and subnets to account for planned usage including scale-out requirements.

-   Ensure the application can scale within the port limits of the chosen application hosting platform. If an application is initiating many outbound TCP or UDP connections it may exhaust all available ports leading to port exhaustion and poor application performance. 

-   Choose SKUs and configure networking services that can meet your bandwidth and availability requirements. VPN Gateway Gateways throughput varies based on SKU. Zone redundancy support is only available for certain Load Balancer SKUs.

-   Ensure that your design for handling DNS is built with a focus on resilience and supports redundant infrastructure.

## Azure facilitation

The Azure platform can help you optimize your resilience through adding redundancy by: 

- Providing built-in redundancy with many PaaS and SaaS services, some of which are configurable.

- Allowing you to design and implement intra-region redundancy through [availability zones](https://learn.microsoft.com/azure/reliability/availability-zones-overview) and inter-region redundancy 
- Offering replica-aware load balancing services like [Application Gateway](https://learn.microsoft.com/azure/application-gateway/), [Azure Front Door](https://learn.microsoft.com/azure/frontdoor/) and [Azure Load Balancer](https://learn.microsoft.com/azure/load-balancer/)
- Offering easily implemented geo-replication solutions like [active geo-replication](https://learn.microsoft.com/azure/azure-sql/database/active-geo-replication-overview?view=azuresql) for Azure SQL Database and Cosmos DB's [global distribution](https://learn.microsoft.com/azure/cosmos-db/distribute-data-globally) and transparent replication. Cosmos DB offers [two options](https://learn.microsoft.com/azure/cosmos-db/conflict-resolution-policies) for handling conflicting writes. Choose the best option for your workload.
- Offering point in time restore capabilities for many PaaS data services.
- Port exhaustion can be mitigated using [Azure NAT Gateway](https://learn.microsoft.com/azure/nat-gateway/nat-overview) or [Azure Firewall](https://learn.microsoft.com/azure/firewall/overview) 

### DNS-specific Azure facilitation

- For internal name resolution scenarios, use Azure Private DNS Zone which has zone and geo redundancy built in. See [Azure Private DNS zone resiliency](https://learn.microsoft.com/azure/dns/private-dns-resiliency)
- For external name resolution scenarios, use Azure Public DNS Zone which has zone and geo redundancy built in.
- Azure public/private DNS is a global service which is resilient to regional outages because zone data is globally available
- For hybrid name resolution scenarios between on-premises and cloud, use Azure DNS Private Resolver. It supports zone redundancy as long as it is located in a region that supports availability zones. In case of a zone wide outage, no action is required during zone recovery and the service will self-heal and rebalance to take advantage of the healthy zone automatically. See [Resiliency in Azure DNS Private Resolver](https://learn.microsoft.com/azure/dns/private-resolver-reliability)
- To eliminate a single point of failure and achieve a more resilient hybrid name resolution across regions, deploy two or more Azure DNS Private Resolvers across different regions. DNS failover, in a conditional forwarding scenario, is then achieved by assigning a resolver as your primary DNS server and the resolver in a different region as a secondary DNS server. See [Tutorial - Set up DNS failover using private resolvers](https://learn.microsoft.com/azure/dns/tutorial-dns-private-resolver-failover)

## Tradeoff

- Additional workload redundancy equates to additional costs, so adding redundancy must be carefully considered and the architecture must be regularly reviewed to ensure that costs are controlled. This is especially true of overprovisioning. When using overprovisioning as a resiliency strategy, balance it out with a well-defined scaling strategy (link to RE:05) to minimize cost inefficiencies.
- There can be performance tradeoffs when building in a high degree of redundancy. For example, having resources spread across availability zones or regions can affect performance by having to send traffic between redundant resources like web servers or database instances over high latency connections.
- Different flows within the same workload may have different reliability requirements, so adding flow-specific redundancy designs can potentially introduce additional complexity to the overall design.

## Example

Refer to the [reference architecture](https://learn.microsoft.com/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant) for an example of a multi-region redundant deployment.

:::image type="content" source="../_images/redundancy/reliable-web-app-dotnet.png" alt-text="Diagram that shows the architecture of the reference implementation." border="false" lightbox="../_images/redundancy/reliable-web-app-dotnet.png":::

## Related links

To learn more about stateful data service redundancy, see these links:

-   [Azure Data Lake Gen2 data redundancy options](https://learn.microsoft.com/azure/storage/common/storage-redundancy)

-   [Azure Cosmos DB high availability guidance](https://learn.microsoft.com/azure/storage/common/storage-redundancy)

-   [Azure SQL Database active geo-replication](https://learn.microsoft.com/azure/azure-sql/database/active-geo-replication-overview?view=azuresql)
- [Azure SQL Managed Instance replication](https://learn.microsoft.com/azure/azure-sql/managed-instance/replication-between-two-instances-configure-tutorial?view=azuresql)
