---
title:  
description:  
author:  
ms.author:  
ms.date: 11/15/2023
ms.topic: conceptual
ms.custom:
  - guide
---

# Best practices for highly available multi-region design

## Applies to RE:04

This guide describes the best practices for designing a highly available multi-region cloud environment. High availability is a foundational tenet of designing for reliability. A highly available architecture will help you avoid downtime as much as possible and recover efficiently if downtime occurs.

Active/active and active/passive architectures are general terms that can be applied in different ways depending on the platform that you deploy your environment on. This guide focuses on a multi-region cloud environment design, but in the case of Azure, you can also design an active/active or active/passive architecture within a single region using [availability zones.](https://review.learn.microsoft.com/en-us/azure/reliability/availability-zones-overview) For detailed guidance on designing a highly available architecture using availability zones, refer to the Well-Architected Framework [guide.](https://review.learn.microsoft.com/en-us/azure/well-architected/regions-availability-zones?branch=pr-en-us-771)

## Key design strategies

When designing a highly available (HA) cloud environment, there are two fundamental approaches: active/active and active/passive. Active/active environments are designed to handle production load in every region that you have deployed your workload in, whereas active/passive environments are designed to handle production load only in the primary region and be able to fail over to the secondary (passive) region when necessary. There are design options that you should consider when evaluating each pattern to refine your architecture to meet your business requirements.

Refer to the [deployment stamps design pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/deployment-stamp) article for guidance on architecting your workload in a repeatable, scalable way. Following this design pattern will ensure that your HA design is optimized for efficient management.

The design options of the two patterns include:

**Active/active**

-   Mirrored deployment stamps in two or more Azure regions, each configured to handle production workloads for the region(s) they serve and scalable to handle loads from other regions in the case of a regional outage.

    -   Networking options: use [latency](https://learn.microsoft.com/en-us/azure/frontdoor/routing-methods#latency) or [weighted](https://learn.microsoft.com/en-us/azure/frontdoor/routing-methods#weighted) global routing to spread traffic between regions

    -   Data replication and consistency options: use a globally distributed data store like [Cosmos DB](https://learn.microsoft.com/en-us/azure/cosmos-db/introduction) for multi-region read and write capabilities. For relational databases, take advantage of [readable replicas](https://learn.microsoft.com/en-us/azure/azure-sql/database/read-scale-out?view=azuresql) using read-only connection strings.

    -   Advantage of this design: lower operating costs than an overprovisioned design.

    -   Disadvantage of this design: possible degradation of user experience when scaling up to meet demands of a full load if and when another region experiences an outage.

-   Mirrored deployment stamps in two or more Azure regions, each overprovisioned to handle production workloads for the region(s) they serve and to handle loads from other regions in the case of a regional outage.

    -   Networking options: same as above

    -   Data replication and consistency options: same as above

    -   Advantage of this design: most resilient design possible

    -   Disadvantage of this design: higher operating costs than a scalable design.

-   Common advantages of both designs: high resiliency and low risk of full workload outage

-   Common disadvantages of both designs: higher operating costs and management burden due to the necessity of managing the synchronization of application state and data among other factors.

**Active/passive**

-   One primary region, one or more secondary region(s) with the secondary region minimally deployed (minimum possible compute and data sizing), running without load (known as a "warm spare" region). Upon failover, the compute and data resources will be scaled to handle the load from the primary region.

    -   Networking options: use [priority](https://learn.microsoft.com/en-us/azure/frontdoor/routing-methods#priority) global routing

    -   Data replication and consistency options: Replicate your database to your passive region and use the automatic failover capabilities of PaaS solutions like [Cosmos DB](https://learn.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account#automatic-failover) and [Azure SQL DB](https://learn.microsoft.com/en-us/azure/azure-sql/database/auto-failover-group-sql-db?view=azuresql&tabs=azure-powershell)

    -   Advantage of this design: shortest recovery time for active/passive designs

    -   Disadvantage of this design: highest operating costs for active/passive designs

-   One primary region, one or more secondary region(s) with the secondary region scaled to handle full load, but all compute resources are stopped (known as a "cold spare region"). The resources must be started prior to failover.

    -   Networking options: use [priority](https://learn.microsoft.com/en-us/azure/frontdoor/routing-methods#priority) global routing

    -   Data replication and consistency options: same as the warm spare design

    -   Advantage of this design: lower operating costs than the warm spare design

    -   Disadvantage of this design: longer recovery time than the warm spare design

-   One primary region, one or more secondary region(s) with only the necessary networking deployed in the secondary region. Operators must run provisioning scripts in the secondary region to failover the workloads. This is known as "redeploy on disaster".

    -   Networking options: use [priority](https://learn.microsoft.com/en-us/azure/frontdoor/routing-methods#priority) global routing

    -   Data replication and consistency options: deploy new database instances and rehydrate the data from backups

    -   Advantage of this design: lowest operating costs

    -   Disadvantage of this design: longest recovery time

-   Common advantages of active/passive designs: lower operating costs and day-to-day management burden than active/active designs; no need to synchronize application state

-   Common disadvantages of active/passive designs: longer, more complex recovery; higher likelihood of needing to perform manual intervention to successfully failover

Note: no matter your high availability design, remember to build redundancy in for your supporting services, like Azure DevOps infrastructure, jump boxes, monitoring or any other critical service necessary to administer the workload.

## Azure facilitation

-   [Azure Front Door](https://review.learn.microsoft.com/en-us/azure/reliability/availability-zones-overview) combines the global routing functionality of Traffic Manager with a content delivery system (CDN) and web application firewall (WAF) to help you seamlessly manage your HA workload.

-   [Cosmos DB](https://learn.microsoft.com/en-us/azure/cosmos-db/introduction) is a globally distributed NoSQL database platform that can help you run an active/active environment and minimize the chance of downtime when a regional outage occurs.

## Tradeoff

*\[Expand on the tradeoff mentioned in the checklist.\]*

## Example

*\[Optional. Include a reference architecture that demonstrates the best practice.\]*

## Related links
