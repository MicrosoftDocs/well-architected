---
title: Azure Well-Architected Framework review for Azure Database for PostgreSQL
description: Design considerations and recommendations for Azure Database for PostgreSQL
author: stephen-sumner
ms.author: ssumner
ms.date: 05/15/2023
ms.topic: conceptual
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

You should review the [design principles](../../cost-optimization/principles.md) to optimize the cost of your architecture.

> [!div class="checklist"]
> - Pick the right tier and SKU
> - Understand high availability mode
> - Scale compute and storage tiers
> - Consider reserved instances
> - Use your provisioned storage
> - Understand geo-redundancy costs
> - Evaluate storage scale up decisions
> - Deploy to same region as app

### Recommendations

Azure Database for PostgreSQL recommendation | Description
| --- | --- |
| Pick the right tier and SKU | Pick the pricing tier and compute SKUs that support the specific needs of your workload. Azure Advisor gives you recommendations to optimize and reduce your overall Azure spending. Recommendations include server right-sizing that you should follow. |
| Understand high availability mode | High availability makes a standby server always available within the same zone or region. Enabling high-availability doubles your cost. |
| Adjust compute and storage tiers | You should adjust the compute and storage tiers manually as needed to meet the requirements of the application over time.
| Use Start/Stop feature | Flexible server has a Start/Stop feature that you can use to stop the server from running when you don't need it.
| Consider reserved instances | Consider a one or three-year reservation to receive significant discounts on compute. Use these reservations for workloads with consistent compute usage for a year or more. |
| Use your provisioned storage | There's no extra charge for backup storage up to 100% of your total provisioned server storage.
| Understand redundancy costs | Geo-redundant storage (GRS) costs twice as much as local redundant storage (LRS). GRS requires double the storage capacity of LRS.
| Evaluate storage scale up decisions | You should evaluate your current and future storage needs before scaling up your storage. After you scale up storage, you can't scale down.
| Deploy to same region as app | Deploy to the same region as the application(s) to minimize transfer costs. When you use virtual network integration, applications in a different virtual network don't have direct access to flexible servers. To grant them access, you need to configure virtual network peering. Virtual network peering has a nominal inbound and outbound data transfer costs.

## Next step

> [!div class="nextstepaction"]
> [Azure SQL Database](azure-sql-database-well-architected-framework.md)
