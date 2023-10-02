---
title: Recommendations for highly available multi-region design 
description: Learn about recommendations that you can apply to designing a highly available multi-region cloud environment.   
author: claytonsiemens77 
ms.author: csiemens 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for highly available multi-region design

**Applies to this Well-Architected Framework Reliability checklist recommendation:**

|[RE:04](checklist.md)| Add redundancy at different levels, especially for critical flows. Apply redundancy to the compute, data, network, and other infrastructure tiers in accordance with the identified reliability targets. |
|---|---|

**Related guides:** [Redundancy](redundancy.md) | [Using availability zones and regions](regions-availability-zones.md) 

This guide describes the recommendations for designing a highly available multi-region cloud environment. High availability is a foundational tenet of designing for reliability. A highly available architecture can help you avoid downtime as much as possible and recover efficiently if downtime does occur.

*Active-active* and *active-passive* are general architecture types that can be applied in different ways, depending on the platform you deploy your environment on. This guide focuses on a multi-region cloud environment design. On Azure, you can also design an active-active or active-passive architecture within a single region by using [availability zones](/azure/reliability/availability-zones-overview). For detailed guidance on designing a highly available architecture by using availability zones, see the [Azure Well-Architected Framework guide](regions-availability-zones.md).

## Key design strategies

Active-active and active-passive are the two fundamental approaches to designing a highly available cloud environment. Active-active environments are designed to handle production loads in every region in which you deploy your workload. Active-passive environments are designed to handle production loads only in the primary region but fail over to the secondary (passive) region when necessary. This section describes design options that you should consider when you evaluate each pattern and refine your architecture to meet your business requirements.

See [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) for guidance on architecting your workload in a repeatable, scalable way. This design pattern can help you optimize your high-availability design for efficient management.

The following sections describe the design options of the two patterns.

### Active-active

-   **Active-active at capacity**: Mirrored deployment stamps in two or more Azure regions, each configured to handle production workloads for the region or regions they serve and scalable to handle loads from other regions in case of a regional outage.

    -   Networking: Use [latency](/azure/frontdoor/routing-methods#latency) or [weighted](/azure/frontdoor/routing-methods#weighted) global routing to spread traffic among regions.

    -   Data replication and consistency: Use a globally distributed data store like [Azure Cosmos DB](/azure/cosmos-db/introduction) for multi-region read and write capabilities. For relational databases, use [readable replicas](/azure/azure-sql/database/read-scale-out) with read-only connection strings.

    -   Advantage of this design: Lower operating costs than an overprovisioned design.

    -   Disadvantage of this design: Possible degradation of the user experience when scaling up to meet the demands of a full load if another region experiences an outage.

-   **Active-active overprovisioned**: Mirrored deployment stamps in two or more Azure regions, each overprovisioned to handle production workloads for the region or regions they serve and to handle loads from other regions in case of a regional outage.

    -   Networking: Use [latency](/azure/frontdoor/routing-methods#latency) or [weighted](/azure/frontdoor/routing-methods#weighted) global routing to spread traffic among regions.

    -   Data replication and consistency: Use a globally distributed data store like [Azure Cosmos DB](/azure/cosmos-db/introduction) for multi-region read and write capabilities. For relational databases, use [readable replicas](/azure/azure-sql/database/read-scale-out) with read-only connection strings.

    -   Advantage of this design: The most resilient design possible.

    -   Disadvantage of this design: Higher operating costs than a scalable design.

-   Common advantages of both designs: High resiliency and low risk of full workload outage.

-   Common disadvantages of both designs: Higher operating costs and management burden due to various factors, including the necessity of managing the synchronization of application state and data.

### Active-passive

-   **Warm spare**: One primary region and one or more secondary regions. The secondary region is deployed with the minimum possible compute and data sizing and runs without load. This region is known as a *warm spare* region. Upon failover, the compute and data resources are scaled to handle the load from the primary region.

    -   Networking: Use [priority](/azure/frontdoor/routing-methods#priority) global routing.

    -   Data replication and consistency: Replicate your database to your passive region and use the automatic failover capabilities of platform as a service (PaaS) solutions like [Azure Cosmos DB](/azure/cosmos-db/how-to-manage-database-account#automatic-failover) and [Azure SQL Database](/azure/azure-sql/database/auto-failover-group-sql-db).

    -   Advantage of this design: Shortest recovery time among the active-passive designs.

    -   Disadvantage of this design: Highest operating cost among the active-passive designs.

-   **Cold spare**: One primary region and one or more secondary regions. The secondary region is scaled to handle full load, but all compute resources are stopped. This region is known as a *cold spare* region. You need to start the resources before failover.

    -   Networking: Use [priority](/azure/frontdoor/routing-methods#priority) global routing.

    -   Data replication and consistency: Replicate your database to your passive region and use the automatic failover capabilities of PaaS solutions like [Azure Cosmos DB](/azure/cosmos-db/how-to-manage-database-account#automatic-failover) and [Azure SQL Database](/azure/azure-sql/database/auto-failover-group-sql-db).

    -   Advantage of this design: Lower operating costs than the warm spare design.

    -   Disadvantage of this design: Longer recovery time than the warm spare design.

-   **Redeploy on disaster**: One primary region and one or more secondary regions. Only the necessary networking is deployed in the secondary region. Operators must run provisioning scripts in the secondary region to fail over the workloads. This design is known as *redeploy on disaster*.

    -   Networking: Use [priority](/azure/frontdoor/routing-methods#priority) global routing.

    -   Data replication and consistency: Deploy new database instances and rehydrate the data from backups.

    -   Advantage of this design: Lowest operating costs.

    -   Disadvantage of this design: Longest recovery time.

-   Common advantages of active-passive designs: Lower operating costs and less day-to-day management burden than active-active designs. No need to synchronize application state.

-   Common disadvantages of active-passive designs: Longer, more complex recovery process. Higher likelihood of needing manual intervention for a successful failover.

> [!Note] 
> Regardless of your high-availability design, remember to configure redundancy for supporting services like Azure DevOps infrastructure, jump boxes, monitoring, and any other critical service that's necessary to administer the workload.

## Azure facilitation

-   [Azure Front Door](https://azure.microsoft.com/products/frontdoor) combines the global routing functionality of Azure Traffic Manager with a content delivery system and web application firewall to help you manage your high-availability workload.

-   [Azure Cosmos DB](/azure/cosmos-db/introduction) is a globally distributed NoSQL database platform that can help you run an active-active environment and minimize the chance of downtime when a regional outage occurs.

## Related links

- [Multi-region N-tier application](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
- [Multi-region load balancing ](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md)  