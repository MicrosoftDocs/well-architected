---
title: Azure Well-Architected Framework review - Azure SQL Database 
description: Focuses on the Azure SQL Database service to provide best-practice, configuration recommendations, and design considerations.
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.date: 06/23/2022
ms.topic: conceptual
products:
  - azure-sql-database
categories:
  - data
  - management-and-governance
---

# Azure Well-Architected Framework review - Azure SQL Database

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service (PaaS) database engine that handles most of the database management functions without user involvement. Management functions include upgrades, patches, backups, and monitoring.

The single database resource type creates a database in Azure SQL Database with its own set of resources and is managed via a [logical server](/azure/azure-sql/database/logical-servers). You can choose between the [DTU-based purchasing model](/azure/azure-sql/database/service-tiers-dtu) or [vCore-based purchasing model](/azure/azure-sql/database/service-tiers-vcore). You can create multiple databases in a single resource pool, with [elastic pools](/azure/azure-sql/database/elastic-pool-overview).

The following sections include a design checklist and recommended design options specific to Azure SQL Database security. The guidance is based on the five pillars of architectural excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

## Prerequisites

* Understanding the Well-Architected Framework pillars can help produce a high quality, stable, and efficient cloud architecture. Check out the [Azure Well-Architected Framework overview page](../../index.md) to review the five pillars of architectural excellence.

* Review the [core concepts of Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) and [What's new in Azure SQL Database?](/azure/azure-sql/database/doc-changes-updates-release-notes-whats-new).

## Azure SQL Database and reliability

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service (PaaS) database engine that handles most of the database management functions without user involvement. Management functions include:

- Upgrades
- Patches
- Backups
- Monitoring

This service allows you to create a highly available and high-performance data storage layer for your Azure applications and workloads. Azure SQL Database is always running on the latest stable version of the SQL Server database engine and patched OS with `99.99%` availability.

For more information about how Azure SQL Database promotes reliability and enables your business to continue operating during disruptions, reference [Availability capabilities](/azure/azure-sql/database/sql-database-paas-overview#availability-capabilities).

The following sections include design considerations, a configuration checklist, and recommended configuration options specific to Azure SQL Database and reliability.

### Design considerations

Azure SQL Database includes the following design considerations:

- Azure SQL Database Business Critical tier configured with geo-replication has a guaranteed Recovery time objective (RTO) of `30` seconds for `100%` of deployed hours.
- Use *sharding* to distribute data and processes across many identically structured databases. Sharding provides an alternative to traditional scale-up approaches for cost and elasticity. Consider using sharding to partition the database horizontally. Sharding can provide fault isolation. For more information, reference [Scaling out with Azure SQL Database](/azure/azure-sql/database/elastic-scale-introduction).
- Azure SQL Database Business Critical or Premium tiers not configured for Zone Redundant Deployments, General Purpose, Standard, or Basic tiers, or Hyperscale tier with two or more replicas have an availability guarantee. For more information about the availability guarantee, reference [SLA for Azure SQL Database](https://azure.microsoft.com/support/legal/sla/azure-sql-database/v1_6/).
- Provides built-in regional high availability and turnkey geo-replication to any Azure region. It includes intelligence to support self-driving features, such as:
  - Performance tuning
  - Threat monitoring
  - Vulnerability assessments
  - Fully automated patching and updating of the code base

- Define an application performance SLA and monitor it with alerts. Quickly detect when your application performance inadvertently degrades below an acceptable level, which is important to maintain high resiliency. Use the monitoring solution previously defined to set alerts on key query performance metrics so you can take action when the performance breaks the SLA. Go to [Monitor Your Database](/azure/azure-sql/database/monitor-tune-overview) and [alerting tools](/azure/azure-sql/database/alerts-insights-configure-portal) for more information.
- Use geo-restore to recover from a service outage. You can restore a database on any SQL Database server or an instance database on any managed instance in any Azure region from the most recent geo-replicated backups. Geo-restore uses a geo-replicated backup as its source. You can request geo-restore even if the database or datacenter is inaccessible because of an outage. Geo-restore restores a database from a geo-redundant backup. For more information, reference [Recover an Azure SQL database using automated database backups](/azure/azure-sql/database/recovery-using-backups).
- Use the Business Critical tier configured with geo-replication, which has a guaranteed Recovery point objective (RPO) of `5` seconds for `100%` of deployed hours.
- PaaS capabilities built into Azure SQL Database enable you to focus on the domain-specific database administration and optimization activities that are critical for your business.
- Use point-in-time restore to recover from human error. Point-in-time restore returns your database to an earlier point in time to recover data from changes done inadvertently. For more information, read the [Point-in-time restore (PITR)](/azure/azure-sql/database/recovery-using-backups#point-in-time-restore) documentation.
- Business Critical or Premium tiers are configured as Zone Redundant Deployments which have an availability guarantee. For more information about the availability guarantee, reference [SLA for Azure SQL Database](https://azure.microsoft.com/support/legal/sla/azure-sql-database/v1_6/).

### Checklist

**Have you configured Azure SQL Database with reliability in mind?**
***

> [!div class="checklist"]
> - Use Active Geo-Replication to create a readable secondary in a different region.
> - Use Auto Failover Groups that can include one or multiple databases, typically used by the same application.
> - Use a Zone-Redundant database.
> - Monitor your Azure SQL Database in near-real time to detect reliability incidents.
> - Implement Retry Logic.
> - Back up your keys.

### Configuration recommendations

Explore the following table of recommendations to optimize your Azure SQL Database configuration for reliability:

|Recommendation|Description|
|--------------|-----------|
|Use Active Geo-Replication to create a readable secondary in a different region.|If your primary database fails, perform a manual failover to the secondary database. Until you fail over, the secondary database remains read-only. [Active geo-replication](/azure/azure-sql/database/active-geo-replication-overview) enables you to create readable replicas and manually failover to any replica if there is a datacenter outage or application upgrade. Up to four secondaries are supported in the same or different regions, and the secondaries can also be used for read-only access queries. The failover must be initiated manually by the application or the user. After failover, the new primary has a different connection end point.|
|Use Auto Failover Groups that can include one or multiple databases, typically used by the same application.|You can use the readable secondary databases to offload read-only query workloads. Because autofailover groups involve multiple databases, these databases must be configured on the primary server. Autofailover groups support replication of all databases in the group to only one secondary server or instance in a different region. Learn more about [AutoFailover Groups](/azure/azure-sql/database/auto-failover-group-overview?tabs=azure-powershell) and [DR design](/azure/azure-sql/database/designing-cloud-solutions-for-disaster-recovery).|
|Use a Zone-Redundant database.|By default, the cluster of nodes for the premium availability model is created in the same datacenter. With the introduction of Azure Availability Zones, SQL Database can place different replicas of the Business Critical database to different availability zones in the same region. To eliminate a single point of failure, the control ring is also duplicated across multiple zones as three gateway rings (GW). The routing to a specific gateway ring is controlled by [Azure Traffic Manager (ATM)](/azure/traffic-manager/traffic-manager-overview). Because the zone redundant configuration in the Premium or Business Critical service tiers doesn't create extra database redundancy, you can enable it at no extra cost. Learn more about [Zone-redundant databases](/azure/azure-sql/database/high-availability-sla).|
|Monitor your Azure SQL Database in near-real time to detect reliability incidents.|Use one of the available solutions to monitor SQL DB to detect potential reliability incidents early and make your databases more reliable. Choose a near real-time monitoring solution to quickly react to incidents. Reference [Azure SQL Analytics](/azure/azure-monitor/insights/azure-sql#analyze-data-and-create-alerts) for more information.|
|Implement Retry Logic.|Although Azure SQL Database is resilient when it concerns transitive infrastructure failures, these failures might affect your connectivity. When a transient error occurs while working with SQL Database, make sure your code can retry the call. For more information, reference [how to implement retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues).|
|Back up your keys.|If you're not [using encryption keys in Azure Key Vault to protect your data](/azure/azure-sql/database/always-encrypted-azure-key-vault-configure?tabs=azure-powershell), back up your keys.|


## Azure SQL Database and security

SQL Database provides a range of [built-in security and compliance](/azure/azure-sql/database/security-overview) features to help your application meet various security and compliance requirements.

### Design checklist

**Have you designed your workload and configured Azure SQL Database with security in mind?**
***

> [!div class="checklist"]
> - Understand [logical servers](/azure/azure-sql/database/logical-servers) and how you can administer logins for multiple databases when appropriate.
> - Enable [Azure AD authentication with Azure SQL](/azure/azure-sql/database/authentication-aad-configure). Azure AD authentication enables simplified permission management and centralized identity management.
> - Azure SQL logical servers should have [an Azure Active Directory administrator provisioned](/azure/azure-sql/database/authentication-aad-configure#azure-ad-admin-with-a-server-in-sql-database). 
> - Verify contact information email address in your Azure Subscription for service administrator and co-administrators is reaching the correct parties inside your enterprise. You don't want to miss or ignore important security notifications from Azure!
> - Review the [Azure SQL Database connectivity architecture](/azure/azure-sql/database/connectivity-architecture). Choose the `Redirect` or `Proxy` [connection policy](/azure/azure-sql/database/connectivity-architecture#connection-policy) as appropriate.
> - Review [Azure SQL Database firewall rules](/azure/azure-sql/database/firewall-configure).
> - Use [virtual network rules](/azure/azure-sql/database/vnet-service-endpoint-rule-overview) to control communication from particular subnets in virtual networks.
> - If using the Azure Firewall, [configure Azure Firewall application rules with SQL FQDNs](/azure/firewall/sql-fqdn-filtering).

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Review [the minimum TLS version](/azure/azure-sql/database/connectivity-settings#minimal-tls-version). | Determine whether you have legacy applications that require older TLS or unencrypted connections. After you enforce a version of TLS, it's not possible to revert to the default. Review and configure the minimum TLS version for SQL Database connections via the Azure portal. If not, set the latest TLS version to the minimum. |
| Ledger | Consider designing database tables based on the [Ledger](/sql/relational-databases/security/ledger/ledger-overview) to provide auditing, tamper-evidence, and trust of all data changes. |
| Always Encrypted | Consider designing application access based around [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) to protect sensitive data inside applications by delegating data access to encryption keys. |
| Private endpoints and private link | [Private endpoint connections](/azure/private-link/private-endpoint-overview) enforce secure communication by enabling private connectivity to Azure SQL Database. You can use a private endpoint to secure connections and deny public network access by default. [Azure Private Link for Azure SQL Database](/azure/azure-sql/database/private-endpoint-overview) is a type of private endpoint recommended for Azure SQL Database. |
| Automated vulnerability assessments | Monitor for [vulnerability assessment](/azure/azure-sql/database/sql-vulnerability-assessment) scan results and recommendations for how to remediate database vulnerabilities. |
| Advanced Threat Protection | Detect anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases with [Advanced Threat Protection for Azure SQL Database](/azure/azure-sql/database/threat-detection-configure). Advanced Threat Protection integrates its alerts with [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/). |
| Auditing | Track database events with [Auditing for Azure SQL Database](/azure/azure-sql/database/auditing-overview).|
| Managed identities | Consider configuring [a user-assigned managed identity (UMI)](/azure/azure-sql/database/authentication-azure-ad-user-assigned-managed-identity). [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities) eliminate the need to manage credentials in code. |
| Azure AD-only authentication | Consider disabling SQL-based authentication and [allowing only on Azure AD authentication](/azure/azure-sql/database/authentication-aad-configure). |


### Policy definitions

Review the [Azure security baseline for Azure SQL Database](/security/benchmark/azure/baselines/sql-database-security-baseline) and [Azure Policy built-in definitions](/azure/azure-sql/database/policy-reference).

All built-in policy definitions related to Azure SQL are listed in [Built-in policies](/azure/governance/policy/samples/built-in-policies#sql).

Review [Tutorial: Secure a database in Azure SQL Database](/azure/azure-sql/database/secure-database-tutorial).


## Azure SQL Database and cost optimization

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service (PaaS) database engine that handles most of the database management functions without user involvement. Management functions include:

- Upgrades
- Patches
- Backups
- Monitoring

This service allows you to create a highly available and high-performance data storage layer for your Azure applications and workloads. SQL Database includes built-in intelligence that helps you dramatically reduce the costs of running and managing databases through automatic performance monitoring and tuning.

For more information about how Azure SQL Database provides cost-saving features, reference [Plan and manage costs for Azure SQL Database](/azure/azure-sql/database/cost-management).

The following sections include a configuration checklist and recommended configuration options specific to Azure SQL Database and cost optimization.

### Checklist

**Have you configured Azure SQL Database with cost optimization in mind?**
***

> [!div class="checklist"]
> - Optimize queries.
> - Evaluate resource usage.
> - Fine-tune [backup storage consumption](/azure/azure-sql/database/automated-backups-overview#fine-tune-backup-storage-consumption).
> - Evaluate [Azure SQL Database serverless](/azure/azure-sql/database/serverless-tier-overview).
> - Consider [reserved capacity for Azure SQL Database](/azure/azure-sql/database/reserved-capacity-overview).
> - Consider [elastic pools for managing and scaling multiple databases](/azure/azure-sql/database/elastic-pool-overview).

### Configuration recommendations

Explore the following table of recommendations to optimize your Azure SQL Database configuration for cost savings:

|Recommendation|Description|
|--------------|-----------|
|Optimize queries.|Optimize the queries, tables, and databases using [Query Performance Insights](/azure/azure-sql/database/query-performance-insight-use) and [Performance Recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal) to help reduce resource consumption, and arrive at appropriate configuration.|
|Evaluate resource usage.|Evaluate the resource usage for all databases and determine if they've been sized and provisioned correctly. For non-production databases, consider scaling resources down as applicable. The DTUs or vCores for a database can be scaled on demand, for example, when running a load test or user acceptance test. |
|Fine-tune backup storage consumption | For vCore databases in Azure SQL Database, the storage consumed by each type of backup (full, differential, and log) is reported on the database monitoring pane as a separate metric. Backup storage consumption up to the maximum data size for a database is not charged. Excess backup storage consumption will depend on the workload and maximum size of the individual databases. For more information, see [Backup storage consumption](/azure/azure-sql/database/automated-backups-overview#backup-storage-consumption). |
|Evaluate Azure SQL Database Serverless.|Consider using [Azure SQL Database serverless](/azure/azure-sql/database/serverless-tier-overview) over the Provisioned Computing Tier. Serverless is a compute tier for single databases that automatically scales compute based on workload demand and bills for the amount of compute used per second. The serverless compute tier also automatically pauses databases during inactive periods when only storage is billed. It automatically resumes databases when activity returns. Azure SQL Database serverless isn't suited for all scenarios. If you have a database with unpredictable or bursty usage patterns interspersed with periods of low or idle usage, serverless is a solution that can help you optimize price-performance.|
|Consider reserved capacity for Azure SQL Database.|You can reduce compute costs associated with Azure SQL Database by using [Reservation Discount](/azure/cost-management-billing/reservations/understand-reservation-charges). Once you've determined the total compute capacity and performance tier for Azure SQL databases in a region, you can use this information to reserve the capacity. The reservation can span one or three years. For more information, reference [Save costs for resources with reserved capacity](/azure/azure-sql/database/reserved-capacity-overview).|
|Elastic pools help you manage and scale multiple databases in Azure SQL Database| Azure SQL Database elastic pools are a simple, cost-effective solution for managing and scaling multiple databases that have varying and unpredictable usage demands. The databases in an elastic pool are on a single server and share a set number of resources at a set price. For more information, see [Elastic pools for managing and scaling multiple databases](/azure/azure-sql/database/elastic-pool-overview).|

For more information, see [Plan and manage costs for Azure SQL Database](/azure/azure-sql/database/cost-management).

## Azure SQL Database and operational excellence

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service (PaaS) database engine that handles most of the database management functions without user involvement. Management functions include:

- Upgrades
- Patches
- Backups
- Monitoring

This service allows you to create a highly available and high-performance data storage layer for your Azure applications and workloads. Azure SQL Database provides advanced monitoring and tuning capabilities backed by artificial intelligence to help you troubleshoot and maximize the performance of your databases and solutions.

For more information about how Azure SQL Database promotes operational excellence and enables your business to continue operating during disruptions, reference [Monitoring and performance tuning in Azure SQL Database](/en-us/azure/azure-sql/database/monitor-tune-overview).

The following sections include design considerations, a configuration checklist, and recommended configuration options specific to Azure SQL Database, and operational excellence.

### Design considerations

Azure SQL Database includes the following design considerations:

- Azure SQL Database Business Critical tier configured with geo-replication has a guaranteed Recovery time objective (RTO) of `30` seconds for `100%` of deployed hours.
- Use *sharding* to distribute data and processes across many identically structured databases. Sharding provides an alternative to traditional scale-up approaches for cost and elasticity. Consider using sharding to partition the database horizontally. Sharding can provide fault isolation. For more information, reference [Scaling out with Azure SQL Database](/azure/azure-sql/database/elastic-scale-introduction).
- Azure SQL Database Business Critical or Premium tiers not configured for Zone Redundant Deployments, General Purpose, Standard, or Basic tiers, or Hyperscale tier with two or more replicas have an availability guarantee. For more information, reference [SLA for Azure SQL Database](https://azure.microsoft.com/support/legal/sla/azure-sql-database/v1_6/).
- Provides built-in regional high availability and turnkey geo-replication to any Azure region. It includes intelligence to support self-driving features, such as:
  - Performance tuning
  - Threat monitoring
  - Vulnerability assessments
  - Fully automated patching and updating of the code base

- Define an application performance SLA and monitor it with alerts. Quickly detect when your application performance inadvertently degrades below an acceptable level, which is important to maintain high resiliency. Use the monitoring solution previously defined to set alerts on key query performance metrics so you can take action when the performance breaks the SLA. Go to [Monitor Your Database](/azure/azure-sql/database/monitor-tune-overview) for more information.
- Use geo-restore to recover from a service outage. You can restore a database on any SQL Database server or an instance database on any managed instance in any Azure region from the most recent geo-replicated backups. Geo-restore uses a geo-replicated backup as its source. You can request geo-restore even if the database or datacenter is inaccessible because of an outage. Geo-restore restores a database from a geo-redundant backup. For more information, reference [Recover an Azure SQL database using automated database backups](/azure/azure-sql/database/recovery-using-backups).
- Use the Business Critical tier configured with geo-replication, which has a guaranteed Recovery point objective (RPO) of `5` seconds for `100%` of deployed hours.
- PaaS capabilities built into Azure SQL Database enable you to focus on the domain-specific database administration and optimization activities that are critical for your business.
- Use point-in-time restore to recover from human error. Point-in-time restore returns your database to an earlier point in time to recover data from changes done inadvertently. For more information, read the [Point-in-time restore (PITR)](/azure/azure-sql/database/recovery-using-backups#point-in-time-restore) documentation.
- Business Critical or Premium tiers are configured as Zone Redundant Deployments. For more information about the availability guarantee, reference [SLA for Azure SQL Database](https://azure.microsoft.com/support/legal/sla/azure-sql-database/v1_6/) .

### Checklist

**Have you configured Azure SQL Database with operational excellence in mind?**
***

> [!div class="checklist"]
> - Use Active Geo-Replication to create a readable secondary in a different region.
> - Use Auto Failover Groups that can include one or multiple databases, typically used by the same application.
> - Use a Zone-Redundant database.
> - Monitor your Azure SQL Database in near-real time to detect reliability incidents.
> - Implement retry logic.
> - Back up your keys.

### Configuration recommendations

Explore the following table of recommendations to optimize your Azure SQL Database configuration for operational excellence:

|Recommendation|Description|
|--------------|-----------|
|Use Active Geo-Replication to create a readable secondary in a different region.|If your primary database fails, perform a manual failover to the secondary database. Until you fail over, the secondary database remains read-only. [Active geo-replication](/azure/azure-sql/database/active-geo-replication-overview) enables you to create readable replicas and manually failover to any replica if there is a datacenter outage or application upgrade. Up to four secondaries are supported in the same or different regions, and the secondaries can also be used for read-only access queries. The failover must be initiated manually by the application or the user. After failover, the new primary has a different connection end point.|
|Use Auto Failover Groups that can include one or multiple databases, typically used by the same application.|You can use the readable secondary databases to offload read-only query workloads. Because autofailover groups involve multiple databases, these databases must be configured on the primary server. Autofailover groups support replication of all databases in the group to only one secondary server or instance in a different region. Learn more about [Auto-Failover Groups](/azure/azure-sql/database/auto-failover-group-overview?tabs=azure-powershell) and [DR design](/azure/azure-sql/database/designing-cloud-solutions-for-disaster-recovery).|
|Use a Zone-Redundant database.|By default, the cluster of nodes for the premium availability model is created in the same datacenter. With the introduction of Azure Availability Zones, SQL Database can place different replicas of the Business Critical database to different availability zones in the same region. To eliminate a single point of failure, the control ring is also duplicated across multiple zones as three gateway rings (GW). The routing to a specific gateway ring is controlled by [Azure Traffic Manager (ATM)](/azure/traffic-manager/traffic-manager-overview). Because the zone redundant configuration in the Premium or Business Critical service tiers doesn't create extra database redundancy, you can enable it at no extra cost. Learn more about [Zone-redundant databases](/azure/azure-sql/database/high-availability-sla).|
|Monitor your Azure SQL Database in near-real time to detect reliability incidents.|Use one of the available solutions to monitor SQL DB to detect potential reliability incidents early and make your databases more reliable. Choose a near real-time monitoring solution to quickly react to incidents. Reference [Azure SQL Analytics](/azure/azure-monitor/insights/azure-sql#analyze-data-and-create-alerts) for more information.|
|Implement Retry Logic.|Although Azure SQL Database is resilient when it concerns transitive infrastructure failures, these failures might affect your connectivity. When a transient error occurs while working with SQL Database, make sure your code can retry the call. For more information, reference [how to implement retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues) and [Configurable retry logic in SqlClient introduction](/sql/connect/ado-net/configurable-retry-logic-sqlclient-introduction).|
|Back up your keys.|If you're not [using encryption keys in Azure Key Vault to protect your data](/azure/azure-sql/database/always-encrypted-azure-key-vault-configure?tabs=azure-powershell), back up your keys.|


## Azure SQL Database and performance efficiency

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service (PaaS) database engine that handles most of the database management functions without user involvement. Management functions include:

- Upgrades
- Patches
- Backups
- Monitoring

The following sections include a design checklist and recommended design options specific to Azure SQL Database performance efficiency.

### Design checklist

**Have you designed your workload and configured Azure SQL Database with performance efficiency in mind?**
***

> [!div class="checklist"]
> - Review resource limits. For specific resource limits per pricing tier (also known as service objective) for single databases, refer to either [DTU-based single database resource limits](/azure/azure-sql/database/resource-limits-dtu-single-databases) or [vCore-based single database resource limits](/azure/azure-sql/database/resource-limits-vcore-single-databases). For elastic pool resource limits, refer to either [DTU-based elastic pool resource limits](/azure/azure-sql/database/resource-limits-dtu-elastic-pools) or [vCore-based elastic pool resource limits](/azure/azure-sql/database/resource-limits-vcore-elastic-pools).
> - Choose the right deployment model for your workload, vCore or DTU. [Compare the vCore and DTU-based purchasing models](/azure/azure-sql/database/purchasing-models).
> - Microsoft recommends the latest vCore database standard-series or premium-series hardware. Older Gen4 hardware has been retired.
> - When using elastic pools, familiarize yourself with [resource governance](/azure/azure-sql/database/elastic-pool-resource-management).
> - Review the [default max degree of parallelism (MAXDOP)](/azure/azure-sql/database/configure-max-degree-of-parallelism) and configure as needed based on a migrated or expected workload.
> - Consider using [read-only replicas](/azure/azure-sql/database/read-scale-out) of critical database to offload read-only query workloads.
> - Review the [Performance Center for SQL Server Database Engine and Azure SQL Database](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database).
> - Applications connecting to Azure SQL Database should use the latest connection providers, for example the latest [OLE DB Driver](/sql/connect/oledb/oledb-driver-for-sql-server) or [ODBC Driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Diagnose and troubleshoot high CPU utilization. | Azure SQL Database provides built-in tools to [identify the causes of high CPU usage and to optimize workload performance](/azure/azure-sql/database/high-cpu-diagnose-troubleshoot). |
| Understand blocking and deadlocking issues. | [Blocking due to concurrency](/azure/azure-sql/database/understand-resolve-blocking) and [terminated sessions due to deadlocks](/azure/azure-sql/database/analyze-prevent-deadlocks) have different causes and outcomes. |
| Tune applications and databases for performance. | Tune your application and database to improve performance. [Review best practices](/azure/azure-sql/database/performance-guidance).|
| Review Azure portal utilization reporting and scale as appropriate. | After deployment, use built-in reporting in the Azure portal to regularly review peak and average database utilization and right-size up or down. You can easily scale [single databases](/azure/azure-sql/database/single-database-scale) or [elastic pools](/azure/azure-sql/database/elastic-pool-scale) with [no data loss and minimal downtime](/azure/azure-sql/database/scale-resources). |
| Review Performance Recommendations. | In the [Intelligent Performance menu](/azure/azure-sql/database/intelligent-insights-overview) of the database page in the Azure portal, review and consider action on any of the [Performance Recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal) and [implement any index, schema, and parameterization issues](/azure/azure-sql/database/database-advisor-implement-performance-recommendations). |
| Review Query Performance Insight. | Review [Query Performance Insight for Azure SQL Database](/azure/azure-sql/database/query-performance-insight-use) reports to identify top resource-consuming queries, long running queries, and more. |
| Configure [Automatic tuning](/azure/azure-sql/database/automatic-tuning-overview). | Provide peak performance and stable workloads through continuous performance tuning based on AI and machine learning. Consider using Azure Automation to configure [email notifications for automatic tuning](/azure/azure-sql/database/automatic-tuning-email-notifications-configure). |
| Evaluate potential use of in-memory database objects. | [In-memory technologies](/azure/azure-sql/in-memory-oltp-overview) enable you to improve performance of your application, and potentially reduce cost of your database. Consider designing some database objects in high-volume OLTP applications. |
| Leverage the [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). | Enabled by default in Azure SQL Database, the Query Store contains a wealth of [query performance and resource consumption](/sql/relational-databases/performance/tune-performance-with-the-query-store) data, as well as advanced tuning features like [Query Store hints](/sql/relational-databases/performance/query-store-hints) and [automatic plan correction](/sql/relational-databases/automatic-tuning/automatic-tuning). Review [Query Store defaults in Azure SQL Database](/sql/relational-databases/performance/best-practice-with-the-query-store). |
| Implement retry logic for transient errors. | Applications should include [automatic transaction retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues) for transient errors including common connection errors. Leverage exponential retry interval logic. |


## Additional resources

For information about supported features, see [Features](/azure/azure-sql/database/features-comparison) and [Resolving Transact-SQL differences during migration to SQL Database](/azure/azure-sql/database/transact-sql-tsql-differences-sql-server).

Migrating to Azure SQL Database? Review our [Azure Database Migration Guides](/data-migration/).

Watch episodes of [Data Exposed](/shows/data-exposed/) covering Azure SQL topics and more.


## Next steps

* [Try Azure SQL Database free with Azure free account](/azure/azure-sql/database/free-sql-db-free-account-how-to-deploy), then [get started with single databases in Azure SQL Database](/azure/azure-sql/database/quickstart-content-reference-guide).
