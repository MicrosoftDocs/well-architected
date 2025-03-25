---
title: Architecture Best Practices for Azure SQL Database 
description: Focuses on the Azure SQL Database service to provide best-practice, configuration recommendations, and design considerations.
author: claytonsiemens77
ms.author: csiemens
ms.date: 03/21/2025
ms.topic: conceptual
ms.service: azure-waf
products:
  - azure-sql-database
azure.category:
  - databases

---

# Architecture best practices for Azure SQL Database

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service (PaaS) database engine that handles most of the database management functions without user involvement. Management functions include upgrades, patches, backups, and monitoring.

The single database resource type creates a database in Azure SQL Database with its own set of resources and is managed via a [logical server](/azure/azure-sql/database/logical-servers). You can choose between the [DTU-based purchasing model](/azure/azure-sql/database/service-tiers-dtu) or [vCore-based purchasing model](/azure/azure-sql/database/service-tiers-vcore). You can create multiple databases in a single resource pool, with [elastic pools](/azure/azure-sql/database/elastic-pool-overview).

This article assumes that as an architect, you've reviewed the [core concepts of Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) and [What's new in Azure SQL Database?](/azure/azure-sql/database/doc-changes-updates-release-notes-whats-new).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Azure SQL. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations:
> [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant)

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure SQL Database

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](../reliability/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the reliability of Azure SQL Database. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> - Familiarize yourself with Azure SQL Database product reliability guidance. For more information, see the following resources:
>    - [Business continuity overview](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview)
>    - [High availability](/azure/azure-sql/database/high-availability-sla-local-zone-redundancy)
>    - [HA/DR checklist](/azure/azure-sql/database/high-availability-disaster-recovery-checklist)
>    - [Automated backups](/azure/azure-sql/database/automated-backups-overview)
> - Use the Business Critical tier for critical workloads. The Business Critical tier offers the highest reliability guarantees of all SKUs.
> - Enhance the availability of your databases by using Active Geo-Replication and Failover Groups and zone-redundancy.
> - Use geo-restore to recover from a service outage. You can restore a database on any SQL Database server or an instance database on any managed instance in any Azure region from the most recent geo-replicated backups.
> - Use point-in-time restore to recover from human error. Point-in-time restore returns your database to an earlier point in time to recover data from inadvertent changes.
> - Monitor your Azure SQL Database in near-real time to detect reliability incidents.
> - Implement retry logic with backoff logic to handle transient faults in your application.
> - When using your own (customer-managed) keys for transparent data encryption (TDE), backup the keys to Azure Key Vault.

### Recommendations

Explore the following table of recommendations to optimize your Azure SQL Database configuration for reliability:

|Recommendation|Benefit|
|--------------|-----------|
|Use [Active Geo-Replication](/azure/azure-sql/database/active-geo-replication-overview) to create a readable secondary in a different region.|If your primary database fails, you can perform a manual failover to the replicated database, making it the new primary instance. If your application supports read-only connection strings, you can take advantage of the secondary database, enhancing the performance and resiliency of your application.|
|Use [Failover Groups](/azure/azure-sql/database/auto-failover-group-overview) to automate failover from primary to secondary database instances.| Failover groups provide read-write and read-only listener end-points that remain unchanged during geo-failovers. You don't have to change the connection string for your application after a geo-failover, because connections are automatically routed to the current primary. A geo-failover switches all secondary databases in the group to the primary role. After geo-failover completes, the DNS record is automatically updated to redirect the endpoints to the new region.|
|Configure your database for [zone-redundnacy](/azure/azure-sql/database/high-availability-sla-local-zone-redundancy).|Zone-redundant availability ensures your data is spread across three Azure availability zones in the primary region. Each availability zone is a separate physical location with independent power, cooling, and networking.|
|Implement [retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues).|Although Azure SQL Database is resilient to transitive infrastructure failures, these failures might affect your connectivity. When a transient error occurs while working with SQL Database, make sure your code can retry the call. Implement backoff logic into your retry logic.|
|[Back up your encryption keys](/azure/azure-sql/database/transparent-data-encryption-byok-overview) when using your own keys.|Managing your own encryption keys has several benefits like full and granular control of the keys, but you also have responsibility for managing the keys and key rotation. Using Azure Key Vault allows you to centrally manage all of your keys in one tool and protects you from inadvertantly losing a key. | 

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Service Fabric.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> - Review the [built-in security and compliance features](/azure/azure-sql/database/security-overview) article for a review of features that can help your application meet your security and compliance requirements.
> - Review the [security baseline](/security/benchmark/azure/baselines/azure-sql-security-baseline) for Azure SQL Database for recommended controls.
> - Centralize the administration of logins, firewall rules, auditing rules, and threat detection policies for collections of databases at the [logical server](/azure/azure-sql/database/logical-servers) level.
> - Enable [Microsoft Entra authentication with Azure SQL](/azure/azure-sql/database/authentication-aad-configure) for centralized authorization and access management.
> - Use [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver16) and [Always Encrypted with secure enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves?view=sql-server-ver16) to protect highly sensitive information like credit card numbers and social security numbers.
> - Review the [Azure SQL Database connectivity architecture](/azure/azure-sql/database/connectivity-architecture). Choose the `Redirect` or `Proxy` [connection policy](/azure/azure-sql/database/connectivity-architecture#connection-policy) when practical.
> - Configure [Azure SQL Database firewall rules](/azure/azure-sql/database/firewall-configure) at the database and server level to restrict access to databases.
> - Use [virtual network rules](/azure/azure-sql/database/vnet-service-endpoint-rule-overview) to control communication from particular subnets in virtual networks.
> - If using an Azure Firewall, [configure Azure Firewall application rules with SQL FQDNs](/azure/firewall/sql-fqdn-filtering).

### Recommendations

| Recommendation|Benefit|
|--------|----|
| Configure [the minimum TLS version](/azure/azure-sql/database/connectivity-settings#minimal-tls-version) to at least version 1.2, and if practical 1.3. | TLS versions 1.0 and 1.1 have been determined to be insecure and will be deprecated in Azure. Version 1.3 is the latest and most secure version. |
| Consider designing database tables based on the [ledger](/sql/relational-databases/security/ledger/ledger-how-to-configure-ledger-database). | The ledger functionality provides auditing, tamper-evidence, and trust for all data changes. | 
| Configure [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) to protect sensitive data inside applications by delegating data access to encryption keys. | Always Encrypted ensures that encryption keys are never exposed to the Database Engine. This provides a separation between those who own the data and can view it, and those who manage the data but should have no access: on-premises database administrators, cloud database operators, or other high-privileged unauthorized users. |
| Extend the capabilities of Always Encrypted by enabling [secure enclaves](/azure/azure-sql/database/always-encrypted-enclaves-getting-started?view=azuresql&viewFallbackFrom=sql-server-ver16). | Secure enclaves allow you to perform otherwise unsupported operations in Always Encrypted databases inside a secure memory enclave on the server side. |
| Use [Azure Private Link for Azure SQL Database](/azure/azure-sql/database/private-endpoint-overview) to enforce secure communication over [private endpoints](/azure/private-link/private-endpoint-overview). | Private Link provides private connectivity between your database and virtual network, allowing you to disable public access.  |
| Scan for vulernabilities with the Microsoft Defender for Azure SQL [vulnerability assessment](/azure/azure-sql/database/sql-vulnerability-assessment). | SQL vulnerability assessment is a built-in service for Azure SQL Database that identifies and helps remediate potential security vulnerabilities by providing actionable steps and customized remediation scripts based on Microsoft's best practices. |
| Detect anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases with [Advanced Threat Protection for Azure SQL Database](/azure/azure-sql/database/threat-detection-configure). | Advanced Threat Protection enables you to detect and respond to potential threats as they occur by providing security alerts on anomalous activities. Alerts are integrated into Microsoft Defender for Cloud. |
| Track database events with [Auditing for Azure SQL Database](/azure/azure-sql/database/auditing-overview).| Auditing helps you maintain regulatory compliance, understand database activity, and gain insight into discrepancies and anomalies that could indicate business concerns or suspected security violations. |
| Configure [a user-assigned managed identity (UMI)](/azure/azure-sql/database/authentication-azure-ad-user-assigned-managed-identity) as the server identity. | Managed identities for Azure resources eliminate the need to manage credentials in code. |
| Disable SQL-based authentication and [allow only on Microsoft Entra authentication](/azure/azure-sql/database/authentication-aad-configure). | Using Microsoft Entra for authentication centralizes your identity, access, and authorization management and provides granular permissions to Azure SQL Database resources. |

### Policy definitions

Review the [Azure security baseline for Azure SQL Database](/security/benchmark/azure/baselines/sql-database-security-baseline) and [Azure Policy built-in definitions](/azure/azure-sql/database/policy-reference).

All built-in policy definitions related to Azure SQL are listed in [Built-in policies](/azure/governance/policy/samples/built-in-policies#sql).

Review [Tutorial: Secure a database in Azure SQL Database](/azure/azure-sql/database/secure-database-tutorial).

## Cost optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure SQL Database.

### Design checklist

> [!div class="checklist"]
> - Review the [Plan and manage costs for Azure SQL Database](/azure/azure-sql/database/cost-management) article.
> - Optimize queries and other operations to reduce resource consumption, minimize execution time, and improve overall performance.
> - Incorporate cost optimization considerations into your database scaling strategies.
> - Fine-tune backup storage consumption to avoid charges for excess utilization.
> - Evaluate [Azure SQL Database serverless](/azure/azure-sql/database/serverless-tier-overview) and [Azure SQL Database Hyperscale](/azure/azure-sql/database/service-tier-hyperscale) to better align pricing with use case.
> - Consider [reserved capacity for Azure SQL Database](/azure/azure-sql/database/reserved-capacity-overview) for static workloads that you intend to operate for longer timescales.
> - Consider [elastic pools for managing and scaling multiple databases](/azure/azure-sql/database/elastic-pool-overview).

### Recommendations

Explore the following table of recommendations to optimize your Azure SQL Database configuration for cost savings:

|Recommendation|Benefit|
|--------------|-----------|
|Optimize your queries, tables, and databases using [Query Performance Insights](/azure/azure-sql/database/query-performance-insight-use) and [Performance Recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal). | Optimizing these operations can help reduce resource consumption and determine the appropriate Azure SQL Database SKUs to provision to meet your performance and budgetary requirements. |
|Intelligently scale your databases to reduce costs, while meeting your performance targets. | You might be able to recover costs by scaling down databases during periods of lighter utilization. Examples include seasonal workloads that might have weeks or months of decreased load or workloads that are idle overnight. In these cases, you can automate scaling operations to match utilization patterns. | 
|Use the recommended strategies discussed in the [Automated backups](/azure/azure-sql/database/automated-backups-overview#backup-storage-consumption) guidance to fine-tune your backup storage consumption. | For vCore databases in Azure SQL Database, the storage consumed by each type of backup (full, differential, and log) is reported on the database monitoring pane as a separate metric. Backup storage consumption up to the maximum data size for a database is included in the price of the database. Excess storage costs depend on the workload and maximum size of the individual databases. |
|Consider using the [serverless](/azure/azure-sql/database/serverless-tier-overview) tier for single databases with intermittent, unpredictable usage patterns that can afford some delay in compute warm-up after idle usage periods. | Serverless databases automatically pause and scale their compute resources based on workload demand. Billing is based on the amount of compute used per second. If you have a database with unpredictable or bursty usage patterns interspersed with periods of low or idle usage, serverless is a solution that can help you optimize price-performance.|
|Consider using the [hyperscale](/azure/azure-sql/database/service-tier-hyperscale) tier for all workload types. | The hyperscale tier's cloud-native architecture provides independently scalable compute and storage to support the widest variety of traditional and modern applications. Compute and storage resources in Hyperscale substantially exceed the resources available in the General Purpose and Business Critical tiers.|
|[Save costs for resources with reserved capacity](/azure/azure-sql/database/reserved-capacity-overview). | You can reduce compute costs associated with Azure SQL Database by pre-purchasing compute resources. Once you've determined the total compute capacity and performance tier for Azure SQL databases in a region, you can use this information to reserve the capacity for one or three years.|
|Use [elastic pools](/azure/azure-sql/database/elastic-pool-overview) to help manage and scale multiple databases. | Azure SQL Database elastic pools are a simple, cost-effective solution for managing and scaling multiple databases that have varying and unpredictable usage demands. The databases in an elastic pool are on a single server and share a set number of resources at a set price. |

## Operational excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure SQL Database.

> [!div class="checklist"]
> - Review the [Monitoring and performance tuning in Azure SQL Database](/en-us/azure/azure-sql/database/monitor-tune-overview) article for detailed monitoring guidance.
> - Use Active Geo-Replication to create a readable secondary in a different region.
> - Use Auto Failover Groups that can include one or multiple databases, typically used by the same application.
> - Use a Zone-Redundant database.
> - Monitor your Azure SQL Database in near-real time to detect reliability incidents.
> - Implement retry logic.
> - Back up your keys.
> - Use Azure Backup to protect an Azure SQL Database server.

### Recommendations

Explore the following table of recommendations to optimize your Azure SQL Database configuration for operational excellence:

|Recommendation|Benefit|
|--------------|-----------|
|Use Active Geo-Replication to create a readable secondary in a different region.|If your primary database fails, perform a manual failover to the secondary database. Until you fail over, the secondary database remains read-only. [Active geo-replication](/azure/azure-sql/database/active-geo-replication-overview) enables you to create readable replicas and manually failover to any replica if there is a datacenter outage or application upgrade. Up to four secondaries are supported in the same or different regions, and the secondaries can also be used for read-only access queries. The failover must be initiated manually by the application or the user. After failover, the new primary has a different connection end point.|
|Use Auto Failover Groups that can include one or multiple databases, typically used by the same application.|You can use the readable secondary databases to offload read-only query workloads. Because autofailover groups involve multiple databases, these databases must be configured on the primary server. Autofailover groups support replication of all databases in the group to only one secondary server or instance in a different region. Learn more about [Auto-Failover Groups](/azure/azure-sql/database/auto-failover-group-overview?tabs=azure-powershell) and [DR design](/azure/azure-sql/database/designing-cloud-solutions-for-disaster-recovery).|
|Use a Zone-Redundant database.|By default, the cluster of nodes for the premium availability model is created in the same datacenter. With the introduction of Azure Availability Zones, SQL Database can place different replicas of the Business Critical database to different availability zones in the same region. To eliminate a single point of failure, the control ring is also duplicated across multiple zones as three gateway rings (GW). The routing to a specific gateway ring is controlled by [Azure Traffic Manager (ATM)](/azure/traffic-manager/traffic-manager-overview). Because the zone redundant configuration in the Premium or Business Critical service tiers doesn't create extra database redundancy, you can enable it at no extra cost. Learn more about [Zone-redundant databases](/azure/azure-sql/database/high-availability-sla).|
|Monitor your Azure SQL Database in near-real time to detect reliability incidents.|Use one of the available solutions to monitor SQL DB to detect potential reliability incidents early and make your databases more reliable. Choose a near real-time monitoring solution to quickly react to incidents. Reference [Azure SQL Analytics](/azure/azure-monitor/insights/azure-sql#analyze-data-and-create-alerts) for more information.|
|Implement Retry Logic.|Although Azure SQL Database is resilient when it concerns transitive infrastructure failures, these failures might affect your connectivity. When a transient error occurs while working with SQL Database, make sure your code can retry the call. For more information, reference [how to implement retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues) and [Configurable retry logic in SqlClient introduction](/sql/connect/ado-net/configurable-retry-logic-sqlclient-introduction).|
|Back up your keys.|If you're not [using encryption keys in Azure Key Vault to protect your data](/azure/azure-sql/database/always-encrypted-azure-key-vault-configure?tabs=azure-powershell), back up your keys.|
|Use Azure Backup to protect an Azure SQL Database server. | Using Azure Backup to protect your Azure SQL Database server enables you to centrally manage business continuity and disaster recovery from the Recovery Services vault, retain recovery points for long term storage, and perform [Cross Region Restore](/azure/backup/restore-sql-database-azure-vm#cross-region-restore) and [Cross Subscription Restore](/azure/backup/restore-sql-database-azure-vm#cross-subscription-restore). With the [built-in data protection capabilities] in the Azure Recovery Services vault](/azure/backup/backup-azure-recovery-services-vault-overview#key-features), you can prevent any accidental or malicious deletion of backups. |

## Performance efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Azure SQL Database.

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

| Recommendation|Benefit|
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
