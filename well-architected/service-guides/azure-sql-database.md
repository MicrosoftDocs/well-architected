---
title: Architecture Best Practices for Azure SQL Database 
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure SQL Database.
author: claytonsiemens77
ms.author: csiemens
ms.date: 08/17/2025
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-service-guide
products:
  - azure-sql-database
azure.category:
  - databases
---

# Architecture best practices for Azure SQL Database

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service database engine that handles most database management functions without user involvement. Management functions include upgrades, patches, backups, and monitoring.

The single database resource type creates a database in SQL Database. The database has its own set of resources and is managed via a [logical server](/azure/azure-sql/database/logical-servers). You can create multiple databases in a single resource pool by using [elastic pools](/azure/azure-sql/database/elastic-pool-overview).

This article assumes that as an architect, you've reviewed the [data store options](/azure/architecture/data-guide/technology-choices/data-store-classification) and chose SQL Database as the database engine for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

This article also assumes that you're familiar with the SQL Database core concepts. For more information, see [Core concepts of SQL Database](/azure/azure-sql/database/sql-database-paas-overview) and [What's new in SQL Database?](/azure/azure-sql/database/doc-changes-updates-release-notes-whats-new).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- SQL Database

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](../reliability/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the reliability of SQL Database. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Familiarize yourself with SQL Database product reliability guidance:**
>   For more information, see the following resources:
>   - [Business continuity overview](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview)
>   - [High availability](/azure/azure-sql/database/high-availability-sla-local-zone-redundancy)
>   - [High availability and disaster recovery checklist](/azure/azure-sql/database/high-availability-disaster-recovery-checklist)
>   - [Automated backups](/azure/azure-sql/database/automated-backups-overview)
> - **Choose appropriate SKU configurations:** Use the Business Critical tier for critical workloads because it offers the highest reliability guarantees.
>
>   Consider the SQL Database Hyperscale tier to meet strict recovery time objective and recovery point objective targets when the Business Critical tier isn't practical. The Hyperscale tier uses storage snapshots rather than traditional database backup mechanisms, which provide zero downtime and rapid recovery.
>
>   If you consistently reach database connection limits (concurrent workers, logins, and sessions) using optimized code, evaluate if migrating a different SKU would help.
> - **Build redundancy to improve resiliency:** Enhance the availability of your databases by using active geo-replication, failover groups, and zone-redundancy.
> - **Use native disaster recovery and backup features:** Use geo-restore to recover from a service outage. You can restore a database on any SQL Database server or a managed instance in any Azure region. Restoration uses the most recent geo-replicated backups.
>
>   Use point-in-time restore to recover from human error. Point-in-time restore returns your database to an earlier point in time to recover data from inadvertent changes.
> - **Monitor reliability and overall health indicators of SQL Database:** Monitor SQL Database in near real-time to detect reliability incidents. Use [`sys.event_log`](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database) to troubleshoot connection failures or deadlocks. Configure alerts for failed connections.
> - **Isolate connection pools in your client code:** Ensure that [connection pools](/azure/azure-sql/database/performance-guidance#optimize-connectivity-and-connection-pooling) are isolated for distinct functional areas within your application. To isolate a pool, modify the connection string for a specific module, such as by adding a unique `Application Name` (for example, `Application Name=BackgroundReporting` versus `Application Name=UserTraffic`).
>
>   This isolation ensures that a resource-intensive task, like a background job, doesn't consume all available connections and block critical user-facing operations, which mitigates cascading failures within that client instance.
> - **Implement retry logic and backoff logic:** Use these features to handle transient faults in your application.
> - **Shard data:** Replicate your schema across multiple databases, storing a subset of your data in each instance. [Sharding](/azure/azure-sql/database/elastic-scale-introduction#sharding) can provide fault isolation, where the failure of one database instance does not impact the availability of other instances in the pool.
> - **Back up your TDE encryption keys:** When you use customer-managed keys for Transparent Data Encryption (TDE), back up the keys to Azure Key Vault.

### Configuration recommendations

|Recommendation|Benefit|
|--------------|-----------|
|Use [active geo-replication](/azure/azure-sql/database/active-geo-replication-overview) to create a readable secondary database in a different region.</br></br> If your application supports read-only connection strings, use the secondary database to handle read-only database actions. This method reduces burden on the primary instance.| If your primary database fails, you can perform a manual failover to the replicated database to ensure that your application continues to operate with minimal downtime. A secondary database that has a read-only connection improves overall performance and ensures higher availability for read operations.|
|Use [failover groups](/azure/azure-sql/database/auto-failover-group-overview) to automate failover from primary to secondary database instances. Failover groups provide read-write and read-only listener endpoints that remain unchanged during geo-failovers. A geo-failover switches all secondary databases in the group to the primary role. After geo-failover finishes, the Domain Name System (DNS) record is automatically updated to redirect the endpoints to the new region. | Geo-failover groups simplify the deployment and management of geo-replicated databases, which ensures continuous availability without manual intervention. You don't have to change the connection string for your application after a geo-failover because connections are automatically routed to the new primary database.|
|Configure your database for [zone redundancy](/azure/azure-sql/database/high-availability-sla-local-zone-redundancy).| Zone-redundant availability ensures your compute and storage components are distributed across multiple Azure availability zones within the primary region. Compute and storage components span two or three zones, as selected by Azure SQL Database for optimal resilience, in separate physical locations with independent power, cooling, and networking. If one of the availability zones experiences an outage, the SQL Database instance remains operational and automatically fails over to an operational zone without losing committed data.|
|Implement [retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues) in your applications. SQL Database is resilient to transitive infrastructure failures, but these failures might affect your connectivity. Implement backoff logic into your retry logic.| Retry logic improves an application's resilience to temporary failures and helps the application recover without manual intervention. When the application encounters a transient error while interacting with SQL Database, make sure that your code can retry the call.</br></br> Backoff logic adds delays between retries, which helps prevent network congestion and server overload.|
|Configure [backups](/azure/azure-sql/database/automated-backups-overview) for point-in-time restore (PITR), geo-restore, and long-term retention.| Automated backups provide restore capabilities that you use as part of meeting your workload's [RTO and RPO](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview#rto-and-rpo) requirements. For example, you can use PITR to recover from human error while reserving geo-restore for full service outages. |
|[Back up your encryption keys](/azure/azure-sql/database/transparent-data-encryption-byok-overview) when you use your own keys. When you use your own encryption keys, you get full and granular control of the keys, but you have to manage the keys and key rotation. Key Vault provides a centralized location to manage encryption keys.| Key Vault centrally manages your keys in one tool and helps prevent accidental key loss. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of SQL Database.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines:** To enhance the security posture of your workload, review the [Azure security baseline for SQL Database](/security/benchmark/azure/baselines/sql-database-security-baseline).
>
>   For more information about features that can help workloads meet security and compliance requirements, see [Built-in security and compliance features](/azure/azure-sql/database/security-overview).
> - **Implement strict, conditional, and auditable identity and access management:** Use Microsoft Entra ID for your workload's authentication and authorization needs. Microsoft Entra ID provides centralized authorization and access management.
>
>   Centralize the administration of sign-ins, firewall rules, auditing rules, and threat detection policies for collections of databases at the [logical server](/azure/azure-sql/database/logical-servers) level.
>
> - **Encrypt data:** Enable data encryption to safeguard confidentiality and integrity. Use features like `Always Encrypted` and `Always Encrypted with secure enclaves` to protect highly sensitive information, like credit card numbers and social security numbers. These features help prevent encryption keys from being exposed to the database engine.
>
> - **Apply network segmentation and security controls:** Create intentional segmentation and perimeters in your network design and apply defense-in-depth principles by using localized network controls at all network boundaries.
>
>   Use [virtual network rules](/azure/azure-sql/database/vnet-service-endpoint-rule-overview) to control communication from specific subnets in virtual networks.
>
>   Configure [SQL Database firewall rules](/azure/azure-sql/database/firewall-configure) at the database level and server level to restrict access to databases. If you use an Azure firewall, [configure Azure Firewall application rules with SQL fully qualified domain names (FQDNs)](/azure/firewall/sql-fqdn-filtering).
>
>   Review the [SQL Database connectivity architecture](/azure/azure-sql/database/connectivity-architecture). Use the `Redirect` or `Proxy` [connection policy](/azure/azure-sql/database/connectivity-architecture#connection-policy) when practical.

### Configuration recommendations

| Recommendation|Benefit|
|--------|----|
| Configure the [minimum Transport Layer Security (TLS) version](/azure/azure-sql/database/connectivity-settings#minimal-tls-version) to at least version 1.2, and if practical version 1.3. | Azure is deprecating TLS versions 1.0 and 1.1 because they're insecure. Version 1.3 is the latest and most secure version. |
| Consider designing database tables based on the [ledger](/sql/relational-databases/security/ledger/ledger-how-to-configure-ledger-database). | The ledger functionality provides auditing, tamper-evidence, and trust for all data changes. | 
| Configure [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) to protect sensitive data inside applications by delegating data access to encryption keys. | Always Encrypted helps prevent encryption keys from being exposed to the database engine. This feature provides a separation between people who own the data and should have access to it and people who manage the data but shouldn't have access. For example, on-premises database administrators, cloud database operators, or other high-privileged unauthorized users shouldn't have access. |
| Extend the capabilities of Always Encrypted by enabling [secure enclaves](/azure/azure-sql/database/always-encrypted-enclaves-getting-started). | Use secure enclaves to perform otherwise unsupported operations in Always Encrypted databases, inside a secure memory enclave on the server side. |
| Use [Azure Private Link for SQL Database](/azure/azure-sql/database/private-endpoint-overview) to enforce secure communication over [private endpoints](/azure/private-link/private-endpoint-overview). | Private Link provides private connectivity between your database and virtual network so that you can disable public access.  |
| Scan for vulnerabilities with the Microsoft Defender for SQL Database [vulnerability assessment](/azure/azure-sql/database/sql-vulnerability-assessment). | The SQL vulnerability assessment is a built-in service for SQL Database that identifies and helps remediate potential security vulnerabilities. It provides actionable steps and customized remediation scripts based on Microsoft best practices. |
| Detect anomalous activities by using [advanced threat protection for SQL Database](/azure/azure-sql/database/threat-detection-configure). These activities can indicate unusual and potentially harmful attempts to access or exploit databases. | Advanced threat protection provides security alerts for anomalous activities, which helps you detect and respond to potential threats when they occur. Alerts are integrated into Microsoft Defender for Cloud. |
| Track database events by using [SQL Database auditing](/azure/azure-sql/database/auditing-overview).<br><br>Enable [immutable storage](/azure/azure-sql/database/auditing-overview#remarks) policies for these audit logs.| Auditing helps you maintain regulatory compliance, understand database activity, and gain insight into discrepancies and anomalies that could indicate business concerns or suspected security violations.<br><br>Immutable blob storage ensures Write-Once-Read-Many (WORM) compliance, preventing tampering with audit trails. |
| Configure a [user-assigned managed identity](/azure/azure-sql/database/authentication-azure-ad-user-assigned-managed-identity) as the server identity. | Managed identities for Azure resources eliminate the need to manage credentials in code. |
| Disable SQL-based authentication and [allow Microsoft Entra authentication only](/azure/azure-sql/database/authentication-aad-configure). | Microsoft Entra for authentication centralizes your identity, access, and authorization management and provides granular permissions to SQL Database resources. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to SQL Database.

### Workload design checklist

> [!div class="checklist"]
> - **Familiarize yourself with SQL Database cost management resources:** Review the [Plan and manage costs for SQL Database](/azure/azure-sql/database/cost-management) article. This resource contains cost-saving strategies, including recommendations about how to optimize cost-effective instances and resources and how to choose the right billing model for your workload.
>
> - **Estimate the initial cost:** As part of your cost modeling exercise, use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator) to evaluate the approximate costs associated with SQL Database in your workload.
>
> - **Choose the right SQL Database service tier for your workload:** Evaluate the SQL Database [Serverless](/azure/azure-sql/database/serverless-tier-overview) and [Hyperscale](/azure/azure-sql/database/service-tier-hyperscale) tiers to better align pricing with your use case.
>
>   Consider [elastic pools](/azure/azure-sql/database/elastic-pool-overview) to manage and scale multiple databases.
>
> - **Optimize component costs:** Consider [reserved capacity for SQL Database](/azure/azure-sql/database/reserved-capacity-overview) for static workloads that you intend to operate for long periods of time.
>
>   Fine-tune backup storage consumption to avoid charges for excess usage.
>
> - **Optimize application code costs:** Optimize queries and other operations to reduce resource consumption, minimize runtime, and improve overall performance.
>
> - **Optimize scaling costs:** Incorporate cost optimization considerations into your database scaling strategies.
>
>   To reduce costs, scale down databases during periods of low usage. Examples include seasonal workloads that have weeks or months of decreased load or workloads that are idle overnight.

### Configuration recommendations

|Recommendation|Benefit|
|--------------|-----------|
|Research the available SQL Database [service tiers](/azure/azure-sql/database/sql-database-paas-overview#service-tiers), and select the best model for each use case based on your capacity planning. | An appropriate service tier can help you avoid wasted costs from overprovisioning. |
|Optimize your queries, tables, and databases by using [query performance insight](/azure/azure-sql/database/query-performance-insight-use) and [performance recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal). | Optimized operations can help reduce resource consumption and help you determine the appropriate SQL Database SKUs to meet your performance and budgetary requirements. |
|Use the [recommended backup strategies](/azure/azure-sql/database/automated-backups-overview#backup-storage-consumption) to fine-tune your backup storage consumption.</br></br> For vCore databases in SQL Database, the storage that each type of backup consumes is reported on the database monitoring pane as a separate metric. The backup types include full, differential, and log backups. Backup storage consumption up to the maximum data size for a database is included in the price of the database. Excess storage costs depend on the workload and maximum size of the individual databases.| Understand the factors that influence backup storage costs, such as database size, rate of change, and retention period. Properly configure back storage to optimize your backup strategy and minimize expenses.|
|Consider using the [Serverless](/azure/azure-sql/database/serverless-tier-overview) tier for single databases that have intermittent, unpredictable usage patterns and can afford a delay in compute warm-up after idle usage periods. | Serverless databases automatically pause and scale their compute resources based on workload demand. Billing is based on the amount of compute used per second. If your database has unpredictable or bursty usage patterns with periods of low or idle usage, serverless databases can help optimize cost and performance.|
|Consider using the [Hyperscale](/azure/azure-sql/database/service-tier-hyperscale) tier for all workload types. | The Hyperscale tier's cloud-native architecture provides independently scalable compute and storage to support the widest variety of traditional and modern applications. Compute and storage resources in the Hyperscale tier substantially exceed the resources available in the General Purpose and Business Critical tiers. Hyperscale databases don't require extra SQL licensing costs.|
|[Save costs for resources by using reserved capacity](/azure/azure-sql/database/reserved-capacity-overview). After you determine the total compute capacity and performance tier for Azure SQL databases in a region, use that information to reserve the capacity for one or three years.| Pre-purchase compute resources to reduce compute costs for SQL Database. One-year reservations can save you up to 40%, and three-year reservations can save you up to 60%.|
|Save on SQL licensing costs by exchanging your Software Assurance-enabled SQL Server licenses with [Azure Hybrid Benefit](/azure/azure-sql/azure-hybrid-benefit).| Azure Hybrid Benefit can save you up to 30% on licensing costs in SQL Database. And you can take advantage of your existing Software Assurance agreement as you migrate databases to Azure. |
|Use [elastic pools](/azure/azure-sql/database/elastic-pool-overview) to help manage and scale multiple databases. | SQL Database elastic pools are a simple, cost-effective solution to manage and scale multiple databases that have varying and unpredictable usage demands. The databases in an elastic pool are on a single server and share a set number of resources at a set price. |

## Operational Excellence

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to SQL Database.

> [!div class="checklist"]
>
> - **Familiarize yourself with SQL Database operational excellence resources:** Review the [Monitoring and performance tuning in SQL Database](/azure/azure-sql/database/monitor-tune-overview) article. This article contains detailed monitoring guidance, including recommendations for monitoring query performance, configuring alerts, and using automatic tuning to enhance efficiency.
> - **Use infrastructure as code (IaC) technologies:** Use IaC technologies like [Bicep and Azure Resource Manager templates](/azure/azure-sql/database/single-database-create-bicep-quickstart) to deploy Azure SQL databases to achieve consistent results and take advantage of reusability.
>
>   Use the latest version of the Resource Manager API to take advantage of the newest features, security updates, and performance improvements.
>
> - **Monitor your SQL Database:** Use monitoring to detect reliability incidents and optimize performance. Start by monitoring the CPU and input/output resources that your workload uses. For help designing the reliability and health monitoring solution for your workload, see [Health modeling for workloads](/azure/well-architected/design-guides/health-modeling).
> - **Optimize management of business continuity and disaster recovery:** Use Azure Backup to protect SQL Database and regularly test your backup strategy.
> - **Use native database administration features:** Adopt SQL Database to relieve database administrators of many of their traditional tasks, like infrastructure-related administration, backup management, and high availability and disaster recovery operations. Encourage their growth in cloud-native administration and integration with DevOps teams as they adopt a data-as-code mindset.

### Configuration recommendations

|Recommendation|Benefit|
|--------------|-----------|
|[Monitor SQL Database](/azure/azure-sql/database/monitor-tune-overview) by using tools, such as database watcher, to detect reliability incidents.|Quick detection of reliability incidents enables you to promptly identify and address performance problems, which minimizes disruptions to your workload.|
|Use [continuous integration and continuous deployment workflows](/sql/tools/sql-database-projects/tutorials/create-deploy-sql-project) to streamline the deployment process of SQL projects. | A repeatable workflow to release changes minimizes the need for human intervention and reduces the possibility of errors. This approach ensures a more reliable and efficient deployment process.|
|Use [Azure Backup](/azure/backup/backup-overview) to back up a SQL Database server. |Azure Backup enables centralized management of business continuity and disaster recovery so that you can efficiently oversee your backup operations. Azure Backup supports long-term retention of recovery points to ensure that your data is preserved for extended periods. It also performs cross-region and cross-subscription restores, which enhance the flexibility and resilience of your backup strategy.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for SQL Database.

> [!div class="checklist"]
>
> - **Familiarize yourself with SQL Database performance efficiency resources:** Review the [Performance Center for SQL Server Database Engine and SQL Database](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database) and [Tune applications and databases for performance](/azure/azure-sql/database/performance-guidance) articles. These articles provide insights about how to improve the performance of your SQL Database, including recommendations to optimize server configuration and query performance.
>
> - **Select the right tier, features, and billing model:** Microsoft recommends the latest [vCore-based](/azure/azure-sql/database/service-tiers-sql-database-vcore) purchasing model.
>
>    Review resource limits. Resource limits for singe databases in each pricing tier are also known as *service objectives*. For more information, see [vCore-based single database resource limits](/azure/azure-sql/database/resource-limits-vcore-single-databases). For elastic pool resource limits, see [vCore-based elastic pool resource limits](/azure/azure-sql/database/resource-limits-vcore-elastic-pools).
>
>   Review the [default max degree of parallelism (MAXDOP)](/azure/azure-sql/database/configure-max-degree-of-parallelism), and configure it as needed based on a migrated or expected workload.
>
> - **Optimize workload design and application code for performance:** Consider offloading read-only actions to [read-only replicas](/azure/azure-sql/database/read-scale-out).
>
>   Applications that connect to SQL Database should use the latest connection providers, for example the latest [OLE DB driver](/sql/connect/oledb/oledb-driver-for-sql-server) or [ODBC driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).
>
>   Use connection pooling within your application code. Connection pooling significantly reduces the overhead of establishing new connections. Most drivers, like ADO.NET, enable it by default.[Monitor Azure SQL Database](/azure/azure-sql/database/monitoring-sql-database-azure-monitor) connection performance and resource usage to identify bottlenecks, such as excessive idle connections or insufficient pool limits.
>
>   When you use elastic pools, familiarize yourself with [resource governance](/azure/azure-sql/database/elastic-pool-resource-management).
>
> - **Evaluate auditing scope:** For large OLTP workloads or environments with many databases, consider using database-level auditing instead of server-level auditing. Server-level auditing consolidates logs into a single folder, which can degrade performance.
>
> - **Prefer native SQL functions:** You can improve query performance by reducing application processing overhead while providing SQL standard compliance. This is a concern for international applications that require Unicode support.

### Configuration recommendations

| Recommendation|Benefit|
|--------|----|
| Apply [performance recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal) to optimize your SQL Database or address workload problems. | Use these recommendations to improve database performance.|
| Configure [automatic tuning](/azure/azure-sql/database/automatic-tuning-overview) to automatically optimize your database performance. Automatic tuning is a feature that monitors queries, adapts to changing workloads, and applies tuning recommendations automatically. | Continuous performance tuning based on AI and machine learning can optimize performance and stabilize workloads.|
| Use the [query store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) to help optimize the performance of your queries. The query store helps you quickly find performance differences that query plan changes cause, which simplifies performance troubleshooting. | The query store continuously collects detailed information about all queries. This capability simplifies troubleshooting query performance because you can use this information to quickly diagnose and address query performance problems.|
| Use the [query performance insight](/azure/azure-sql/database/query-performance-insight-use) tool to identify the top resource-consuming and long-running queries in your workload. Query performance insight provides details about database queries by CPU, duration, and implementation count. And you can view the history of resource usage. | Query performance insight provides deep insight into database query metrics such as CPU, duration, and implementation count. This feature reduces the time that you spend troubleshooting database performance.|
| Use built-in tools to [diagnose and troubleshoot high CPU usage](/azure/azure-sql/database/high-cpu-diagnose-troubleshoot). These tools can help identify the causes of high CPU usage and optimize Transact-SQL queries. | Properly tuned applications and databases lead to more efficient operations and potentially lower costs.|
| Understand how [blocking](/azure/azure-sql/database/understand-resolve-blocking) and [deadlocking](/azure/azure-sql/database/analyze-prevent-deadlocks) problems affect your database performance. | Resolving blocking and deadlocking problems helps your database operate more efficiently, eliminates bottlenecks, and improves performance.|
| Review the Azure portal usage reporting, and scale [single databases](/azure/azure-sql/database/single-database-scale) or [elastic pools](/azure/azure-sql/database/elastic-pool-scale) as appropriate. | Regularly review usage reports to ensure that you use your resources efficiently. This practice helps identify underused resources, which can reduce costs.|
| Use [in-memory database objects](/azure/azure-sql/in-memory-oltp-overview) to enhance the performance of your workloads in scenarios like high-performance, online transaction-processing applications and real-time analytics.| In-memory technologies enable you to improve the performance of your workload and potentially reduce database costs.|

## Azure policies

Azure provides an extensive set of built-in policies related to SQL Database. A set of Azure policies can audit some of the preceding recommendations. For example, you can check whether:

- Microsoft Entra-only authentication is enabled by default during creation. 
- Zone redundancy is enabled to enhance availability and resilience.
- Long-term, geo-redundant backup should be enabled for Azure SQL databases.
- Setting the minimal TLS version to 1.2 improves security by ensuring that SQL Database can only be accessed from clients that use TLS 1.2. Don't use earlier versions of TLS because they have well-documented security vulnerabilities.
- SQL servers should have auditing enabled to ensure that you capture the operations performed against your SQL assets.

For comprehensive governance, review the built-in definitions for SQL Database policies that are listed in [Azure Policy built-in definitions](/azure/azure-sql/database/policy-reference).

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

## Related resources

- [Tutorial: Secure a database in SQL Database](/azure/azure-sql/database/secure-database-tutorial)
- [SQL Database features](/azure/azure-sql/database/features-comparison)
- [Resolve Transact-SQL differences during migration to SQL Database](/azure/azure-sql/database/transact-sql-tsql-differences-sql-server)
- [Azure Database Migration Guides](/data-migration/)
- [Video: Data exposed](/shows/data-exposed/)

<!-- Updated: August 17, 2025 for Azure Update 467635, 497258, 498943 -->
