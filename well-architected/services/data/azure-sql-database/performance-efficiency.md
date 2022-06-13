---
title: Azure SQL Database and performance efficiency
description: Focuses on the Azure SQL Database service to provide best-practice, configuration recommendations, and design considerations related to Service Performance.
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.date: 06/13/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-sql-database
categories:
  - data
  - management-and-governance
---

# Azure SQL Database and performance efficiency

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service (PaaS) database engine that handles most of the database management functions without user involvement. Management functions include:

- Upgrades
- Patches
- Backups
- Monitoring

The following sections include a design checklist and recommended design options specific to Azure SQL Database performance efficiency.

## Design checklist

**Have you designed your workload and configured Azure SQL Database with performance efficiency in mind?**
***

> [!div class="checklist"]
> - Review resource limits. For specific resource limits per pricing tier (also known as service objective) for single databases, refer to either [DTU-based single database resource limits](/azure/azure-sql/database/resource-limits-dtu-single-databases) or [vCore-based single database resource limits](/azure/azure-sql/database/resource-limits-vcore-single-databases). For elastic pool resource limits, refer to either [DTU-based elastic pool resource limits](/azure/azure-sql/database/resource-limits-dtu-elastic-pools) or [vCore-based elastic pool resource limits](/azure/azure-sql/database/resource-limits-vcore-elastic-pools).
> - Choose the right deployment model for your workload, vCore or DTU. [Compare the vCore and DTU-based purchasing models](azure/azure-sql/database/purchasing-models).
> - Microsoft recommends the latest vCore database standard-series or premium-series hardware. Older Gen4 hardware has been retired.
> - When using elastic pools, familiarize yourself with [resource governance](/azure/azure-sql/database/elastic-pool-resource-management).
> - Review the [default max degree of parallelism (MAXDOP)](/azure/azure-sql/database/configure-max-degree-of-parallelism) and configure as needed based on a migrated or expected workload.
> - Consider using [read-only replicas](/azure/azure-sql/database/read-scale-out) of critical database to offload read-only query workloads.
> - Review the [Performance Center for SQL Server Database Engine and Azure SQL Database](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database).
> - Applications connecting to Azure SQL Database should use the latest connection providers, for example the latest [OLE DB Driver](/sql/connect/oledb/oledb-driver-for-sql-server) or [ODBC Driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

## Recommendations

| Recommendation | Benefit |
|--------|----|
| Diagnose and troubleshoot high CPU utilization. | Azure SQL Database provides built-in tools to [identify the causes of high CPU usage and to optimize workload performance](/azure/azure-sql/database/high-cpu-diagnose-troubleshoot). |
| Understand blocking and deadlocking issues. | [Blocking due to concurrency](/azure/azure-sql/database/understand-resolve-blocking) and [terminated sessions due to deadlocks](/azure/azure-sql/database/analyze-prevent-deadlocks) have different causes and outcomes. |
| Tune applications and databases for performance. | Tune your application and database to improve performance. [Review best practices](/azure/azure-sql/database/performance-guidance).|
| Review Azure portal utilization reporting and scale as appropriate. | After deployment, use built-in reporting in the Azure portal to regularly review peak and average database utilization and right-size up or down. You can easily scale [single databases](/azure/azure-sql/database/single-database-scale) or [elastic pools](/azure/azure-sql/database/elastic-pool-scale) with [no data loss and minimal downtime](/azure/azure-sql/database/scale-resources). |
| Review Performance Recommendations. | In the [Intelligent Performance menu](/azure/azure-sql/database/intelligent-insights-overview) of the database page in the Azure portal, review and consider action on any the [Performance Recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal) and [implement any index, schema, and parameterization issues](/azure/azure-sql/database/database-advisor-implement-performance-recommendations). |
| Review Query Performance Insight. | Review [Query Performance Insight for Azure SQL Database](/azure/azure-sql/database/query-performance-insight-use) reports to identify top resource-consuming queries, long running queries, and more. |
| Configure [Automatic tuning](/azure/azure-sql/database/automatic-tuning-overview). | Provide peak performance and stable workloads through continuous performance tuning based on AI and machine learning. Consider using Azure Automation to configure [email notifications for automatic tuning](/azure/azure-sql/database/automatic-tuning-email-notifications-configure). |
| Evaluate potential use of in-memory database objects. | [In-memory technologies](/azure/azure-sql/in-memory-oltp-overview) enable you to improve performance of your application, and potentially reduce cost of your database. Consider designing some database objects in high-volume OLTP applications. |
| Leverage the [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). | Enabled by default in Azure SQL Database, the Query Store contains a wealth of [query performance and resource consumption](/sql/relational-databases/performance/tune-performance-with-the-query-store) data, as well as advanced tuning features like [Query Store hints](/sql/relational-databases/performance/query-store-hints) and [automatic plan correction](/sql/relational-databases/automatic-tuning/automatic-tuning). Review [Query Store defaults in Azure SQL Database](/sql/relational-databases/performance/best-practice-with-the-query-store). |
| Implement retry logic for transient errors. | Applications should include [automatic transaction retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues) for transient errors including common connection errors. Leverage exponential retry interval logic. |


## Next step

> [!div class="nextstepaction"]
> [Azure SQL Managed Instance and reliability](../azure-sql-managed-instance/reliability.md)
