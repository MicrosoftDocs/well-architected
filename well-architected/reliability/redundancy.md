---
title: Recommendations for designing for redundancy
description: Learn how to design for redundancy in your workload to meet your reliability targets and to keep problems contained to a single resource.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing for redundancy

**Applies to: RE 04**

This guide describes the recommendations for adding redundancy throughout critical flows at different workload layers, which optimizes resiliency. Meet the requirements of your defined reliability targets by applying the proper levels of redundancy to your compute, data, networking, and other infrastructure tiers. Apply this redundancy to give your workload a strong, reliable foundation to build on. When you build your workload without infrastructure redundancy, there's a high risk of extended downtime due to [potential failures](failure-mode-analysis.md).

**Definitions**

| Term | Definition |
|---------|---------|
| Redundancy | The implementation of multiple identical instances of a workload component. |
| Polyglot persistence | The concept of using different storage technologies by the same application or solution to take advantage of the best capabilities of each component. |
| Data consistency | The measure of how in sync or out of sync a given dataset is across multiple stores. |
| Partitioning | The process of physically dividing data into separate data stores. |
| Shard | A horizontal database partitioning strategy that supports multiple storage instances with a common schema. Data isn't replicated in all instances. |

## Key design strategies

In the context of reliability, use redundancy to contain problems that affect a single resource and ensure that those problems don't affect the reliability of the entire system. Use the information that you identified about your critical flows and reliability targets to make informed decisions that are required for each flow's redundancy.

For example, you might have multiple web server nodes running at once. The criticality of the flow that they support might require that all of them have replicas that are ready to accept traffic if there's a problem that affects the entire pool, for example a regional outage. Alternatively, because large-scale problems are rare and it's costly to deploy an entire set of replicas, you might deploy a limited number of replicas so the flow operates in a degraded state until you resolve the problem.

When you design for redundancy in the context of performance efficiency, distribute the load across multiple redundant nodes to ensure that each node performs optimally. In the context of reliability, build in spare capacity to absorb failures or malfunctions that affect one or more nodes. Ensure that the spare capacity can absorb failures for the entire time that's needed to recover the affected nodes. With this distinction in mind, both strategies need to work together. If you spread traffic across two nodes for performance and they both run at 60 percent utilization and one node fails, your remaining node is at risk of becoming overwhelmed because it can't operate at 120 percent. Spread the load out with another node to ensure that your performance and reliability targets are upheld.

#### Redundant architecture design

Consider two approaches when you design a redundant architecture: active-active or active-passive. Choose your approach depending on the criticality of the user flow and system flow that the infrastructure components support. In terms of reliability, a multi-region active-active design helps you achieve the highest level of reliability possible, but it's significantly more expensive than an active-passive design. You can also use these design approaches for a single region by using availability zones. For more information, see [Recommendations for highly available multi-region design](highly-available-multi-region-design.md).

#### Deployment stamps and units of scale

Whether you deploy in an active-active or active-passive model, follow the [Deployment Stamps design pattern](/azure/architecture/patterns/deployment-stamp) to ensure that you deploy your workload in a repeatable, scalable way. Deployment stamps are the groupings of resources that are required to deliver your workload to a given subset of your customers. For example, the subset might be a regional subset or a subset with all the same data privacy requirements as your workload. Think of each stamp as a *unit of scale* that you can duplicate to scale your workload horizontally or to perform blue-green deployments. Design your workload with deployment stamps to optimize your active-active or active-passive implementation for resiliency and management burden.

#### Availability zones within Azure regions

Whether you deploy an active-active or an active-passive design, take advantage of [availability zones](regions-availability-zones.md) within the active regions to fully optimize your resiliency. Many Azure regions provide multiple availability zones, which are separated groups of data centers within a region. Depending on the Azure service, you can take advantage of availability zones by deploying elements of your workload redundantly across zones or pinning elements to specific zones. For more information, see [Recommendations for using availability zones and regions](regions-availability-zones.md).

### Infrastructure layer guidance

#### Compute resources

- Choose the appropriate [compute service](/azure/architecture/guide/technology-choices/compute-decision-tree) for your workload. Depending on the type of workload that you design, there might be several options available. Research the available services and understand which types of workloads work best on a given compute service. For example, SAP workloads are typically best suited for infrastructure as a service (IaaS) compute services. For a containerized application, determine the specific functionality you need to have control over to determine whether to use Azure Kubernetes Service (AKS) or a platform as a service (PaaS) solution. Your cloud platform fully manages a PaaS service.

- Use PaaS compute options if your requirements allow it. Azure fully manages PaaS services, which reduces your management burden, and a documented degree of redundancy is built in.

- Use Azure Virtual Machine Scale Sets if you need to deploy virtual machines (VMs). With Virtual Machine Scale Sets, you can automatically spread your compute evenly across availability zones.

- Keep your compute layer *clean of any state* because individual nodes that serve requests might be deleted, faulted, or replaced at any time.

- Use zone-redundant services where possible to provide higher resilience without increasing your operational burden.

- Overprovision critical resources to mitigate failures of redundant instances, even before autoscaling operations begin, so the system continues to operate after a component failure. Calculate the acceptable effect of a fault when you incorporate overprovisioning into your redundancy design. As with your redundancy decision-making process, your reliability targets and financial tradeoff decisions determine the extent that you add spare capacity with overprovisioning. Overprovisioning specifically refers to *scaling out*, which means adding extra instances of a given compute resource type, rather than increasing the compute capabilities of any single instance. For example, if you change a VM from a lower-tier SKU to a higher-tier SKU.

- Deploy IaaS services manually or via automation in each availability zone or region in which you intend to implement your solution. Some PaaS services have built-in capabilities that are automatically replicated across availability zones and regions.

#### Data resources

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

#### Networking

- Decide on a reliable and scalable network topology. Use a hub-and-spoke model or an Azure Virtual WAN model to help you organize your cloud infrastructure in logical patterns that make your redundancy design easier to build and scale.

- Select the appropriate [network service](/azure/architecture/guide/technology-choices/load-balancing-overview) to balance and redirect requests within or across regions. Use global or zone-redundant load balancing services when possible to meet your reliability targets.

- Ensure that you have allocated sufficient IP address space in your virtual networks and subnets to account for planned usage, including scale-out requirements.

- Ensure that the application can scale within the port limits of the chosen application hosting platform. If an application initiates several outbound TCP or UDP connections, it might exhaust all available ports and cause poor application performance.

- Choose SKUs and configure networking services that can meet your bandwidth and availability requirements. A VPN gateway's throughput varies based on their SKU. Support for zone redundancy is only available for some load balancer SKUs.

- Ensure that your design for handling DNS is built with a focus on resilience and supports redundant infrastructure.

## Azure facilitation

The Azure platform helps you optimize the resiliency of your workload and add redundancy by:

- Providing built-in redundancy with many PaaS and software as a service (SaaS) solutions, some of which are configurable.

- Allowing you to design and implement intra-region redundancy by using [availability zones](/azure/reliability/availability-zones-overview) and inter-region redundancy.

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

## Tradeoffs

- More workload redundancy equates to more costs. Carefully consider adding redundancy and regularly review your architecture to ensure that you're managing costs, especially when you use overprovisioning. When you use overprovisioning as a resiliency strategy, balance it with a well-defined [scaling strategy](scaling.md) to minimize cost inefficiencies.

- There can be performance tradeoffs when you build in a high degree of redundancy. For example, resources that spread across availability zones or regions can affect performance because you have to send traffic over high-latency connections between redundant resources, like web servers or database instances.

- Different flows within the same workload might have different reliability requirements. Flow-specific redundancy designs can potentially introduce complexity into the overall design.

## Example

For an example of a multi-region redundant deployment, see [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

The following diagram shows another example:
:::image type="content" source="media/redundancy/reliable-web-app-dotnet.png" alt-text="Diagram that shows the architecture of the reference implementation." border="false" lightbox="media/redundancy/reliable-web-app-dotnet.png":::

## Related links

To learn more about stateful data service redundancy, see the following resources:

- [Azure Storage redundancy](/azure/storage/common/storage-redundancy)
- [Zone-redundant storage](/azure/storage/common/storage-redundancy#zone-redundant-storage)
- [Azure SQL Database active geo-replication](/azure/azure-sql/database/active-geo-replication-overview)
- [Configure replication between two managed instances](/azure/azure-sql/managed-instance/replication-between-two-instances-configure-tutorial)
