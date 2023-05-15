---
title: Azure Well-Architected Framework review for Azure Database for PostgreSQL
description: Design considerations and recommendations for Azure Database for PostgrSQL
author: martinekuan
ms.author: martinek
ms.date: 05/15/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-database-postgresql
categories:
  - data
  - management-and-governance
---

# Azure Database for PostgreSQL - Flexible Server

[Azure Database for PostgreSQL](/azure/postgresql/overview) is a relational database service in Azure based on the [PostgreSQL open source relational database](https://www.postgresql.org/). Azure Database for PostgreSQL has two deployment modes: Single Server and Flexible Server

**Single Server** is a fully managed database service that provides high-availability, automated patching, automatic backups, dynamic scalability, and built-in performance monitoring. The Single Server deployment option is best suited for cloud native applications where you don't need to control the patching schedule or use custom PostgreSQL configuration settings.

**Flexible Server** is fully managed database service that provides more control over database management than the Single Server deployment option. You should use the Flexible Server deployment option when you need greater database control, cost optimization features, zone redundancy, and customizable maintenance windows.

This guide provides implementation guidance and recommendations for Azure Database for PostgreSQL - Flexible Server.

## Cost optimization

### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
> -Use Azure Advisor
> -Select the right pricing tier
> -Understand high availability cost
> -Optimize compute cost
> -Optimize storage cost
> -Optimize network cost

### Recommendations

Azure Database for PostgreSQL recommendation | Benefit
| --- | --- |
| Use Azure Advisor | Azure Advisor gives you recommendations to optimize and reduce your overall Azure spending. Recommendations include server right-sizing. |
| Select the right pricing tier | Pick the pricing tier and compute SKUs that support the specific needs of your workload. |
| Understand high availability cost | High availability avoids database downtime by maintaining standby replicas of every node in a server group. If a node goes down, incoming connections are automatically switched from the failed node to its standby. Enabling high-availability doubles your cost. |
| Optimize compute cost | Manually change compute and storage tiers to meet application needs. Use the Start/Stop feature to stop running the server when unneeded. Commit to a one to three year compute reservation to receive significant discounts on compute costs.|
| Optimize storage cost | There's no extra charge for backup storage up to 100% of your total provisioned server storage. Geo-redundant storage (GRS) costs twice local redundant storage. GRS requires twice the amount of storage capacity because it creates a replica. Scaling up storage is possible, but scaling down isn't supported.
| Optimize network | Deploy to the same region as the application(s) to minimize transfer costs. When you use virtual network integration, applications in a different virtual network don't have direct access to flexible servers. You need to configure virtual network peering before they can access the flexible server. Virtual network peering has inbound and outbound data transfer costs.

## Next step

> [!div class="nextstepaction"]
> [Azure SQL Database and reliability](../azure-sql-database/reliability.md)
