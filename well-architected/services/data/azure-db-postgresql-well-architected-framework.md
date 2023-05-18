---
title: Azure Well-Architected Framework review for Azure Database for PostgreSQL
description: Design considerations and recommendations for Azure Database for PostgreSQL
author: stephen-sumner
ms.author: ssumner
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

# Azure Well-Architected Framework review for Azure Database for PostgreSQL

[Azure Database for PostgreSQL](/azure/postgresql/overview) is a relational database service in Azure based on the PostgreSQL open source relational database. Azure Database for PostgreSQL has two deployment modes: Single Server and Flexible Server.

**Single Server** is a fully managed database service that provides high-availability, automated patching, automatic backups, dynamic scalability, and built-in performance monitoring. The Single Server deployment option is best suited for cloud native applications where you don't need to control the patching schedule or use custom PostgreSQL configuration settings.

**Flexible Server** is fully managed database service that provides more control over database management than the Single Server deployment option. You should use the Flexible Server deployment option when you need greater database control, cost optimization features, zone redundancy, and customizable maintenance windows.

This guide provides implementation guidance and recommendations for Azure Database for PostgreSQL. For Azure Cosmos DB for PostgreSQL, see [Build scalable apps in Azure Cosmos DB for PostgreSQL](/azure/cosmos-db/postgresql/quickstart-build-scalable-apps-overview).

## Cost optimization

### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
> - Use Azure Advisor
> - Select the right pricing tier
> - Understand high availability cost
> - Optimize compute cost
> - Optimize storage cost
> - Optimize network cost

### Recommendations

Azure Database for PostgreSQL recommendation | Description
| --- | --- |
| Use Azure Advisor | Azure Advisor gives you recommendations to optimize and reduce your overall Azure spending. Recommendations include server right-sizing that you should follow. |
| Select the right pricing tier | Pick the pricing tier and compute SKUs that support the specific needs of your workload. |
| Understand high availability cost | High availability avoids database downtime by maintaining standby replicas of every node in a server group. If a node goes down, incoming connections are automatically switched from the failed node to its standby. Enabling high-availability doubles your cost. |
| Optimize compute cost | You should manually change compute and storage tiers to meet application needs over time. Flexible server has a Start/Stop feature that you can use to stop the server from running when you don't need it to. We also recommend you use a one- or three-year compute reservation to receive significant discounts on compute costs for workloads that are expected to be in operation for this long. |
| Optimize storage cost | There's no extra charge for backup storage up to 100% of your total provisioned server storage. Geo-redundant storage (GRS) requires twice the amount of storage capacity because it creates a replica. GRS costs twice as much as redundant storage. You should evaluate your current and future storage needs before scaling up your storage. After you scale up your storage, you can't scale down.
| Optimize network cost | Deploy to the same region as the application(s) to minimize transfer costs. When you use virtual network integration, applications in a different virtual network don't have direct access to flexible servers. To grant them access, you need to configure virtual network peering. Virtual network peering has a nominal inbound and outbound data transfer costs.

## Next step

> [!div class="nextstepaction"]
> [Azure SQL Database](./azure-sql-database-well-architected-framework.md)
