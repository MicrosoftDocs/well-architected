---
title: Architecture Strategies for Designing for Redundancy
description: Learn how to design for redundancy in your workload to meet your reliability targets and to keep problems contained to a single resource.
author: claytonsiemens77
ms.author: csiemens
ms.date: 08/13/2025
ms.topic: concept-article
---

# Architecture strategies for designing for redundancy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

| **RE:05** | Add redundancy at different levels, especially for critical flows, to help meet your reliability targets. Consider redundant infrastructure components such as compute and network, and multiple instances of your solution. |
|---|---|

This guide describes the recommendations for adding redundancy throughout critical flows at different workload layers, which optimizes resiliency. Meet the requirements of your defined reliability targets by applying the proper levels of redundancy to your compute, data, networking, and other infrastructure tiers. Apply this redundancy to give your workload a strong, reliable foundation to build on. When you build your workload without infrastructure redundancy, there's a high risk of extended downtime because of [potential failures](failure-mode-analysis.md).

**Definitions**

| Term | Definition |
|---------|---------|
| Redundancy | The implementation of multiple identical instances of a workload component. |
| Region | An [Azure datacenter location](/azure/reliability/regions-list). |
| Polyglot persistence | The concept of using different storage technologies by the same application or solution to take advantage of the best capabilities of each component. |
| Data consistency | The measure of how in sync or out of sync a given dataset is across multiple stores. |
| Partitioning | The process of physically dividing data into separate data stores. |
| Shard | A horizontal database partitioning strategy that supports multiple storage instances with a common schema. Data isn't replicated in all instances. |

In the context of reliability, use redundancy to contain problems that affect a single resource and ensure that those problems don't affect the reliability of the entire system. Use the information that you identified about your critical flows and reliability targets to make informed decisions that are required for each flow's redundancy.

For example, you might have multiple web server nodes that run at once. If the flow that they support is critical, all nodes might need replicas that can accept traffic immediately if a problem affects the entire pool, such as a full datacenter outage. Alternatively, because large-scale problems are rare and it's costly to deploy an entire set of replicas, you might deploy a limited number of replicas so that the flow operates in a degraded state until you resolve the problem.

When you design for redundancy in the context of performance efficiency, distribute the load across multiple redundant nodes to ensure that each node performs optimally. In the context of reliability, build in spare capacity to absorb failures or malfunctions that affect one or more nodes. Ensure that the spare capacity can absorb failures for the entire time that's needed to recover the affected nodes. With this distinction in mind, both strategies need to work together. If you spread traffic across two nodes for performance and they both run at 60% utilization and one node fails, your remaining node is at risk of becoming overwhelmed because it can't operate at 120%. Spread the load out with another node to ensure that your performance and reliability targets are upheld.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-offs:**
>
> - More workload redundancy equates to more costs. Carefully consider adding redundancy and regularly review your architecture to ensure that you're managing costs, especially when you use overprovisioning. When you use overprovisioning as a resiliency strategy, balance it with a well-defined [scaling strategy](scaling.md) to minimize cost inefficiencies.
>
> - There can be performance trade-offs when you build in a high degree of redundancy. For example, resources that spread across availability zones or locations can affect performance because you have to send traffic over high-latency connections between redundant resources, like web servers or database instances.
>
> - Different flows within the same workload might have different reliability requirements. Flow-specific redundancy designs can potentially introduce complexity into the overall design.
>
> - A low-latency redundancy design means that you accept the risk of losing those components in the event of a large-scale event, like a geographic disaster, that takes all instances offline. A geo-distant disaster recovery (DR) environment helps mitigate this risk but increases costs.

## Prefer serverless and fully managed services to reduce operational burden

Take advantage of [serverless](https://azure.microsoft.com/solutions/serverless), software as a service (SaaS), and platform as a service (PaaS) solutions to easily add redundancy to your workload without needing to manage data replication or failover operations. These services implement redundancy transparently, which removes the operational burden of designing and maintaining your own redundancy mechanisms. When you evaluate service options, prioritize managed services that handle redundancy automatically over infrastructure-based approaches that require manual redundancy configuration.

Azure managed services provide redundancy through different models. Each model provides varying levels of abstraction and operational simplicity.

**Global services with built-in redundancy:** Services like Microsoft Entra ID, Azure DNS, and Azure Key Vault operate as global services with automatic redundancy across multiple regions. These services provide inherent resilience without requiring any redundancy configuration from you. They automatically handle failover and recovery scenarios transparently.

**Abstracted capacity models:** Offerings like Azure Cosmos DB, Azure Databricks, and Azure OpenAI managed endpoints abstract the underlying infrastructure complexity behind capacity units, DTUs, or other logical metrics. These services automatically distribute your workload across multiple instances, zones, and regions while presenting a simplified billing and management model. You specify capacity requirements instead of managing individual redundant instances.

When you architect your solution, evaluate whether a managed service option exists for each component before you implement infrastructure-based redundancy. Managed services reduce your operational overhead and often provide more robust redundancy implementations than custom solutions, though they might involve trade-offs in control and potentially higher costs for each unit of capacity.

## Build redundancy into your workload with multiple component instances

Deploy multiple instances of your infrastructure components and services to achieve the resilience that your workload needs. This fundamental redundancy strategy ensures that if one instance fails, other instances can continue to handle the workload. You can achieve multiple instances through different approaches. Some scenarios require you to manually configure redundancy by deploying multiple resources individually, such as multiple Azure ExpressRoute circuits or configuring Azure Traffic Manager in multiple regions. Other services are designed to support multiple instances directly, such as setting Azure Virtual Machine Scale Sets to five instances or configuring Azure App Service to run 10 instances. When possible, prefer services that provide built-in support for multiple instances and autoscaling capabilities because they simplify redundancy management and can respond to both reliability and performance needs.

When you deploy redundant infrastructure components, determine how many instances of each component satisfy your reliability targets. Consider whether you need multiple instances of all components or only specific critical components, and determine the geographical distribution of those instances to meet your resilience requirements. When you deploy redundant infrastructure, consider the following recommendations.

**Compute resources:**

- Prefer services that have support for redundancy built-in. These services allow you to specify the number of instances that you need and might handle the distribution and management of those instances for you.

- Keep your compute layer *clean of any state* because individual nodes that serve requests might be deleted, faulted, or replaced at any time.

- Design and test your scaling strategy to match your redundancy approach.

**Data resources:**

- Replicate data across geographical regions to provide resilience to regional outages and catastrophic failures.

- Understand the built-in [replication and redundancy capabilities of the stateful platform services](#related-links) that you use.

- Determine whether synchronous or asynchronous data replication is necessary for your workload's functionality. For more information, see [Recommendations for using availability zones and regions](../design-guides/regions-availability-zones.md).

- Distribute data geographically, as supported by your service, to minimize the effect of geographically localized failures.

- Automate failover after a database instance failure. You can configure automated failover in multiple Azure PaaS data services. Automated failover isn't required for data stores that support multi-region writes, like Azure Cosmos DB. For more information, see [Recommendations for designing a DR strategy](disaster-recovery.md).

- Use the best [data store](/azure/architecture/databases/) for your data. Embrace polyglot persistence or solutions that use a combination of data store technologies. Data includes more than just persisted application data. It also includes application logs, events, messages, and caches.

- Consider data consistency requirements and use [eventual consistency](/azure/architecture/guide/design-principles/minimize-coordination#recommendations) when requirements allow it. When data is distributed, use appropriate coordination to enforce strong consistency guarantees. Coordination can reduce your throughput and make your systems tightly coupled, which can make them weaker. For example, if an operation updates two databases, instead of putting it into a single transaction scope, it's better if the system can accommodate eventual consistency.

- Partition data for availability. [Database partitioning](/azure/architecture/best-practices/data-partitioning) improves scalability and it can also improve availability. If one shard goes down, the other shards are still reachable. A failure in one shard only disrupts a subset of the total transactions.

- If sharding isn't an option, you can use the [Command Query Responsibility Segregation (CQRS) pattern](/azure/architecture/patterns/cqrs) to separate your read-write and read-only data models. Add more redundant read-only database instances to provide more resilience.  

**Networking resources:**

- Decide on a reliable and scalable network topology. Use a hub-and-spoke model or an Azure Virtual WAN model to help you organize your cloud infrastructure into logical patterns that make your redundancy design easier to build and scale.

- Select the appropriate [network service](/azure/architecture/guide/technology-choices/load-balancing-overview) to balance and redirect requests within or across regions. Use global or zone-redundant load balancing services when possible to meet your reliability targets.

- Ensure that you have allocated sufficient IP address space in your virtual networks and subnets to account for planned usage, including scale-out requirements.

- Ensure that the application can scale within the port limits of the chosen application hosting platform. If an application initiates several outbound Transmission Control Protocol (TCP) or User Datagram Protocol (UDP) connections, it might exhaust all available ports and cause poor application performance.

- Choose SKUs and configure networking services that can meet your bandwidth and availability requirements. A VPN gateway's throughput varies based on their SKU. Support for zone redundancy is only available for some load balancer SKUs.

- Ensure that your design for handling Domain Name System (DNS) is built with a focus on resilience and supports redundant infrastructure.

## Use the Deployment Stamps pattern to streamline your redundancy approach

Beyond redundancy for individual infrastructure components and services, extend your redundancy strategy to the workload level by deploying multiple instances of your entire workload or logical groups of resources. Follow the [Deployment Stamps design pattern](/azure/architecture/patterns/deployment-stamp) to create repeatable, self-contained units that include all the resources required to deliver your workload to a specific subset of users or requirements.

Deployment stamps represent a shift from component-level to workload-level redundancy. Each stamp serves as a complete unit of scale containing all necessary resources—compute, storage, networking, and dependencies—that can operate independently. This approach provides key benefits including isolated failure domains that contain blast radius, horizontal scaling through additional stamp deployment instead of individual component scaling, flexible segmentation by geography or requirements, and simplified operations through identical repeatable units.

Whether you deploy stamps in an active-active model (where all stamps actively serve traffic simultaneously) or active-passive model (where one stamp serves traffic while others remain on standby), design each stamp to be a complete, self-sufficient deployment that can handle its designated workload independently.

## Achieve zero downtime through active-active redundancy

Active-active deployments maximize service availability by running multiple instances of a workload simultaneously, each actively handling traffic. This setup ensures immediate failover, eliminates downtime, and optimizes resource use by keeping all instances productive. If one instance fails, traffic is automatically rerouted to healthy instances without disrupting service.

For example, an e-commerce platform during peak season can maintain seamless operations even if one deployment encounters problems. Active-active configurations are ideal for mission-critical workloads that require uninterrupted availability, but they have higher infrastructure costs and complexity. Advanced operational strategies are required to manage multiple live environments, ensure data consistency, and coordinate updates across all instances.

The following sections describe configuration options for active-active deployments.

- **Active-active at capacity:** Mirrored deployment stamps in two or more locations, each configured to handle production workloads for the location or locations that they serve and scalable to handle loads from other locations if a regional outage occurs.

  - **Networking:** Use [latency](/azure/frontdoor/routing-methods#latency) or [weighted](/azure/frontdoor/routing-methods#weighted) global routing to spread traffic among locations.

  - **Data replication and consistency:** Use a globally distributed data store like [Azure Cosmos DB](/azure/cosmos-db/introduction) for multi-region read and write capabilities. For relational databases, use [readable replicas](/azure/azure-sql/database/read-scale-out) with read-only connection strings.

  - **Advantage of this design:** Lower operating costs than an overprovisioned design.

  - **Disadvantage of this design:** Possible degradation of the user experience when scaling up to meet the demands of a full load if another location experiences an outage.

- **Active-active overprovisioned:** Mirrored deployment stamps in two or more locations, each overprovisioned to handle production workloads for the location or locations that they serve and to handle loads from other locations if a regional outage occurs.

  - **Networking:** Use [latency](/azure/frontdoor/routing-methods#latency) or [weighted](/azure/frontdoor/routing-methods#weighted) global routing to spread traffic among locations.

  - **Data replication and consistency:** Use a globally distributed data store like [Azure Cosmos DB](/azure/cosmos-db/introduction) for multi-region read and write capabilities. For relational databases, use [readable replicas](/azure/azure-sql/database/read-scale-out) with read-only connection strings.

  - **Advantage of this design:** The most resilient design possible.

  - **Disadvantage of this design:** Higher operating costs than a scalable design.

- **Common advantages of both designs:** High resiliency and low risk of full workload outage.

- **Common disadvantages of both designs:** Higher operating costs and management burden because of various factors, including the necessity of managing the synchronization of application state and data.

## Use an active-passive architecture design to provide DR resilience

Active-passive deployment configurations provide a cost-effective way to ensure DR by running a primary instance that handles all traffic while keeping secondary instances idle but ready. These standby instances are activated only when the primary instance fails or undergoes maintenance. This approach minimizes resource usage while providing reliable failover capabilities.

For example, a financial trading platform can use this setup to maintain service continuity. The primary system manages all transactions, while the secondary system remains synchronized in the background. If the primary system goes down, the secondary system quickly takes over and restores operations with minimal disruption. This approach balances reliability and cost, which makes it ideal for workloads that can tolerate brief recovery times without the complexity or expense of active-active deployments.

Consider the following configuration options for active-passive deployments.

- **Warm spare:** One primary location and one or more secondary locations. The secondary location is deployed with the minimum possible compute and data sizing and runs without load. This location is known as a *warm spare* location. After failover, the compute and data resources are scaled to handle the load from the primary location.

  - **Networking:** Use [priority](/azure/frontdoor/routing-methods#priority) global routing.

  - **Data replication and consistency:** Replicate your database to your passive location and use the automatic failover capabilities of PaaS solutions like [Azure Cosmos DB](/azure/cosmos-db/how-to-manage-database-account#automatic-failover) and [Azure SQL Database](/azure/azure-sql/database/auto-failover-group-sql-db).

  - **Advantage of this design:** Shortest recovery time among the active-passive designs.

  - **Disadvantage of this design:** Highest operating cost among the active-passive designs.

- **Cold spare:** One primary location and one or more secondary locations. The secondary location is scaled to handle full load, but all compute resources are stopped. This location is known as a *cold spare* location. You need to start the resources before failover.

  - **Networking:** Use [priority](/azure/frontdoor/routing-methods#priority) global routing.

  - **Data replication and consistency:** Replicate your database to your passive location and use the automatic failover capabilities of PaaS solutions like [Azure Cosmos DB](/azure/cosmos-db/how-to-manage-database-account#automatic-failover) and [SQL Database](/azure/azure-sql/database/auto-failover-group-sql-db).

  - **Advantage of this design:** Lower operating costs than the warm spare design.

  - **Disadvantage of this design:** Longer recovery time than the warm spare design.

## Distribute workloads across proximate independent infrastructure

Deploying workloads across nearby independent datacenters or datacenter sectors provides redundancy without compromising performance. By using geographically close but physically separate facilities, this setup ensures fault isolation with minimal latency impact. It provides the resilience of distributed infrastructure while maintaining the responsiveness of a single-site deployment. In Azure, availability zones provide this functionality. Availability zones are physically independent datacenters or datacenter sectors that are designed to let you easily deploy fault-tolerant, low-latency workloads.

For latency-sensitive applications like real-time gaming, this approach enables seamless failover and uninterrupted user experience. Game servers distributed across local datacenters can instantly reroute traffic during outages, with transparent load balancing and near real-time data replication preserving gameplay continuity. However, this strategy does carry some risk because large-scale regional events can still affect all sites simultaneously.

## Strengthen fault tolerance with geo-distant deployments

Deploying across geographically distributed datacenters provides the strongest protection against large-scale disasters by spreading workloads across regions hundreds or thousands of miles apart. This approach ensures business continuity during events like natural disasters, infrastructure failures, or geopolitical disruptions that can affect entire metropolitan areas. In Azure, you can deploy workloads into regions, which are spread across the globe. When you use this design approach, choose regions that are close to your users but are geographically distant, such as West US and East US.

For example, a global financial platform can maintain uninterrupted service by routing traffic to unaffected regions if one area experiences a major outage. This approach maximizes resilience and supports regulatory compliance across jurisdictions, but it introduces higher latency, complex data consistency requirements, and increased operational overhead. You must weigh these factors carefully against the benefits of global redundancy.

## Azure facilitation

The Azure platform helps you optimize the resiliency of your workload and add redundancy by doing the following actions:

- Helps you select the best Azure regions for your multi-region architecture with the [Select Azure Regions guide](/azure/cloud-adoption-framework/ready/azure-setup-guide/regions).

- Provides built-in redundancy with many PaaS and SaaS solutions, some of which are configurable.

- Provides global managed services like Microsoft Entra ID, Azure DNS, and Key Vault that implement redundancy transparently without requiring configuration.

- Allows you to design and implement intra-region redundancy by using [availability zones](/azure/reliability/availability-zones-overview) and inter-region redundancy.

- Provides zone-redundant compute solutions, like [Virtual Machine Scale Sets](/azure/reliability/reliability-virtual-machine-scale-sets), which can automatically spread your compute evenly across availability zones when you deploy virtual machines (VMs).

- Provides replica-aware load balancing services like [Azure Application Gateway](/azure/application-gateway/), [Azure Front Door](/azure/frontdoor/), and [Azure Load Balancer](/azure/load-balancer/).

- Provides easily implemented geo-replication solutions like [active geo replication](/azure/azure-sql/database/active-geo-replication-overview) for SQL Database. Implement [global distribution](/azure/cosmos-db/distribute-data-globally) and transparent replication by using Azure Cosmos DB. Azure Cosmos DB provides two options for [handling conflicting writes](/azure/cosmos-db/conflict-resolution-policies). Choose the best option for your workload.

- Provides point-in-time restore (PITR) capabilities for many PaaS data services.

- Mitigates port exhaustion via [Azure NAT Gateway](/azure/nat-gateway/nat-overview) or [Azure Firewall](/azure/firewall/overview).

### DNS-specific Azure facilitation

- For internal name resolution scenarios, use Azure DNS private zones, which have built-in zone redundancy and geo redundancy. For more information, see [Azure DNS private zone resiliency](/azure/dns/private-dns-resiliency).

- For external name resolution scenarios, use Azure DNS public zones, which have built-in zone redundancy and geo redundancy.

- The public and private Azure DNS services are global services that are resilient to regional outages because zone data is globally available.

- For hybrid name resolution scenarios between on-premises and cloud environments, use Azure DNS Private Resolver. This service supports zone redundancy if your workload is located in a region that supports availability zones. A zone-wide outage requires no action during zone recovery. The service automatically self-heals and rebalances to take advantage of the healthy zone. For more information, see [Resiliency in Azure DNS Private Resolver](/azure/dns/private-resolver-reliability).

- To eliminate a single point of failure and achieve a more resilient hybrid name resolution across regions, deploy two or more Azure DNS private resolvers across different regions. DNS failover, in a conditional forwarding scenario, is achieved by assigning a resolver as your primary DNS server. Assign the other resolver in a different region as a secondary DNS server. For more information, see [Set up DNS failover by using private resolvers](/azure/dns/tutorial-dns-private-resolver-failover).

### Apply delete protection to preserve redundancy

Redundancy only helps if the components that provide it stay in place. Accidental deletion of a data store, DNS zone, or traffic routing layer collapses resiliency and forces full recovery actions. Reduce human error blast radius by applying Azure resource [*CanNotDelete* locks](/azure/azure-resource-manager/management/lock-resources).

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Applying locks slows legitimate recovery actions and can create operational friction. Locks block resource deletion, not changes or data within the resource. Treat locks as a thin safety net that complements, never replaces, backups, replication, and access governance.

## Example

For an example of a multi-region redundant deployment, see [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

The following diagram shows another example.

:::image type="content" source="media/redundancy/reliable-web-app-dotnet.png" alt-text="Diagram that shows the architecture of the reference implementation." border="false" lightbox="media/redundancy/reliable-web-app-dotnet.png":::

## Related links

To learn more about redundancy, see the following resources:

- [Azure regions guide](/azure/cloud-adoption-framework/ready/azure-setup-guide/regions)
- [Azure Storage redundancy](/azure/storage/common/storage-redundancy)
- [Zone-redundant storage](/azure/storage/common/storage-redundancy#zone-redundant-storage)
- [SQL Database active geo-replication](/azure/azure-sql/database/active-geo-replication-overview)
- [Configure replication between two managed instances](/azure/azure-sql/managed-instance/replication-between-two-instances-configure-tutorial)

## Reliability checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Reliability checklist](checklist.md)
