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

The single database resource type creates a database in Azure SQL Database with its own set of resources and is managed via a [logical server](/azure/azure-sql/database/logical-servers). You can create multiple databases in a single resource pool, with [elastic pools](/azure/azure-sql/database/elastic-pool-overview).

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
> - **Familiarize yourself with Azure SQL Database product reliability guidance:** For more information, see the following resources:
>    - [Business continuity overview](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview)
>    - [High availability](/azure/azure-sql/database/high-availability-sla-local-zone-redundancy)
>    - [HA/DR checklist](/azure/azure-sql/database/high-availability-disaster-recovery-checklist)
>    - [Automated backups](/azure/azure-sql/database/automated-backups-overview)
> - **Choose appropriate SKU configurations:** Use the Business Critical tier for critical workloads as it offers the highest reliability guarantees of all SKUs.
>
>    Consider Azure SQL Database Hyperscale to meet strict RTO and RPO targets when Business Critical isn't practical. Hyperscale's use of storage snapshots rather than traditional database backup mechanisms allow for zero downtime and rapid recovery.
> - **Build redundancy to improve resiliency:** Enhance the availability of your databases by using Active Geo-Replication, Failover Groups, and zone-redundancy.
> - **Use native disaster recovery and backup features:** Use geo-restore to recover from a service outage. You can restore a database on any SQL Database server or an instance database on any managed instance in any Azure region from the most recent geo-replicated backups.
>
>   Use point-in-time restore to recover from human error. Point-in-time restore returns your database to an earlier point in time to recover data from inadvertent changes.
> - **Monitor reliability and overall health indicators of the SQL Database:** Monitor your Azure SQL Database in near-real time to detect reliability incidents.
> - **Implement retry logic with backoff logic:** to handle transient faults in your application.
> - **Backup your TDE encryption keys:** When using your own (customer-managed) keys for transparent data encryption (TDE), backup the keys to Azure Key Vault.

### Recommendations

|Recommendation|Benefit|
|--------------|-----------|
|Use [Active Geo-Replication](/azure/azure-sql/database/active-geo-replication-overview) to create a readable secondary in a different region.</br></br>If your application supports read-only connection strings, you can take advantage of the secondary database to handle read-only database actions, relieving some burden from the primary instance.| If your primary database fails, you can perform a manual failover to the replicated database and ensure that your application continues to operate with minimal downtime. Using the secondary with a read-only connection improves overall performance and ensuring higher availability for read operations.|
|Use [Failover Groups](/azure/azure-sql/database/auto-failover-group-overview) to automate failover from primary to secondary database instances. Failover groups provide read-write and read-only listener endpoints that remain unchanged during geo-failovers. A geo-failover switches all secondary databases in the group to the primary role. After geo-failover completes, the DNS record is automatically updated to redirect the endpoints to the new region. | Using geo-failover groups simplifies the deployment and management of geo-replicated databases, ensuring continuous availability without manual intervention. You don't have to change the connection string for your application after a geo-failover, because connections are automatically routed to the current primary.|
|Configure your database for [zone-redundnacy](/azure/azure-sql/database/high-availability-sla-local-zone-redundancy). Zone-redundant availability ensures your data is spread across three Azure availability zones in the primary region. Each availability zone is a separate physical location with independent power, cooling, and networking.| If one of the availability zones experiences an outage, the Azure SQL Database remains operational and it will automatically failover to an operational zone with zero loss of committed data.| 
|Implement [retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues) in your applications. Although Azure SQL Database is resilient to transitive infrastructure failures, these failures might affect your connectivity. Implement backoff logic into your retry logic.| By implementing retry logic, your application becomes more resilient to temporary failures and may be able to recover without manual intervention. When a transient error occurs while working with SQL Database, make sure your code can retry the call.</br></br> The purpose of the backoff logic is to space out retries, helping prevent network congestion and server overload.|
|[Back up your encryption keys](/azure/azure-sql/database/transparent-data-encryption-byok-overview) when using your own keys. Managing your own encryption keys has several benefits like full and granular control of the keys, but you also have responsibility for managing the keys and key rotation. Azure Key Vault provides a centralized location to manage encryption keys.|Using Azure Key Vault allows you to centrally manage all of your keys in one tool and protects you from inadvertently losing a key. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Service Fabric.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines:** To enhance the security posture of your workload, review the [Azure security baseline for Azure SQL Database](/security/benchmark/azure/baselines/sql-database-security-baseline).
>
>   Review the [built-in security and compliance features](/azure/azure-sql/database/security-overview) article for a review of features that can help the workload meet security and compliance requirements.
> - **Implement strict, conditional, and auditable identity and access management:** Use Microsoft Entra ID for your workload's authentication and authorization needs for centralized authorization and access management.
>
>   Centralize the administration of logins, firewall rules, auditing rules, and threat detection policies for collections of databases at the [logical server](/azure/azure-sql/database/logical-servers) level.
>
> - **Encrypt data:** Enable data encryption to safeguard confidentiality and integrity. Use features like `Always Encrypted` and `Always Encrypted with secure enclaves` to protect highly sensitive information like credit card numbers and social security numbers, ensuring that encryption keys are never exposed to the Database Engine.
>
> - **Apply network segmentation and security controls:** Create intentional segmentation and perimeters in your network design and apply defense in depth principles by using localized network controls at all network boundaries.
>
>   Use [virtual network rules](/azure/azure-sql/database/vnet-service-endpoint-rule-overview) to control communication from particular subnets in virtual networks.
>
>   Configure [Azure SQL Database firewall rules](/azure/azure-sql/database/firewall-configure) at the database and server level to restrict access to databases. If using an Azure Firewall, [configure Azure Firewall application rules with SQL FQDNs](/azure/firewall/sql-fqdn-filtering).
>
>   Review the [Azure SQL Database connectivity architecture](/azure/azure-sql/database/connectivity-architecture). Choose the `Redirect` or `Proxy` [connection policy](/azure/azure-sql/database/connectivity-architecture#connection-policy) when practical.

### Recommendations

| Recommendation|Benefit|
|--------|----|
| Configure [the minimum TLS version](/azure/azure-sql/database/connectivity-settings#minimal-tls-version) to at least version 1.2, and if practical 1.3. | TLS versions 1.0 and 1.1 have been determined to be insecure and will be deprecated in Azure. Version 1.3 is the latest and most secure version. |
| Consider designing database tables based on the [ledger](/sql/relational-databases/security/ledger/ledger-how-to-configure-ledger-database). | The ledger functionality provides auditing, tamper-evidence, and trust for all data changes. | 
| Configure [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) to protect sensitive data inside applications by delegating data access to encryption keys. | Always Encrypted ensures that encryption keys are never exposed to the Database Engine. This provides a separation between those who own the data and should have some degree of access to it, and those who manage the data but shouldn't have any access. For example, on-premises database administrators, cloud database operators, or other high-privileged unauthorized users. |
| Extend the capabilities of Always Encrypted by enabling [secure enclaves](/azure/azure-sql/database/always-encrypted-enclaves-getting-started). | Secure enclaves allow you to perform otherwise unsupported operations in Always Encrypted databases inside a secure memory enclave on the server side. |
| Use [Azure Private Link for Azure SQL Database](/azure/azure-sql/database/private-endpoint-overview) to enforce secure communication over [private endpoints](/azure/private-link/private-endpoint-overview). | Private Link provides private connectivity between your database and virtual network, allowing you to disable public access.  |
| Scan for vulnerabilities with the Microsoft Defender for Azure SQL [vulnerability assessment](/azure/azure-sql/database/sql-vulnerability-assessment). | SQL vulnerability assessment is a built-in service for Azure SQL Database that identifies and helps remediate potential security vulnerabilities by providing actionable steps and customized remediation scripts based on Microsoft's best practices. |
| Detect anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases with [Advanced Threat Protection for Azure SQL Database](/azure/azure-sql/database/threat-detection-configure). | Advanced Threat Protection enables you to detect and respond to potential threats as they occur by providing security alerts on anomalous activities. Alerts are integrated into Microsoft Defender for Cloud. |
| Track database events with [Auditing for Azure SQL Database](/azure/azure-sql/database/auditing-overview).| Auditing helps you maintain regulatory compliance, understand database activity, and gain insight into discrepancies and anomalies that could indicate business concerns or suspected security violations. |
| Configure [a user-assigned managed identity (UMI)](/azure/azure-sql/database/authentication-azure-ad-user-assigned-managed-identity) as the server identity. | Managed identities for Azure resources eliminate the need to manage credentials in code. |
| Disable SQL-based authentication and [allow Microsoft Entra authentication only](/azure/azure-sql/database/authentication-aad-configure). | Using Microsoft Entra for authentication centralizes your identity, access, and authorization management and provides granular permissions to Azure SQL Database resources. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure SQL Database.

### Design checklist

> [!div class="checklist"]
> - **Familiarize yourself with the [Plan and manage costs for Azure SQL Database](/azure/azure-sql/database/cost-management) article:** This resource contains cost-saving strategies, including recommendations on using more cost-effective instances, optimizing resources, and choosing the right billing model for your workload.
> - **Estimate the initial cost:** As part of your cost modeling exercise, use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator) to evaluate the approximate costs associated with the use of Azure SQL Database in your workload.
> - **Choose the right Azure SQL Database service tier for your workload:** Evaluate [Azure SQL Database serverless](/azure/azure-sql/database/serverless-tier-overview) and [Azure SQL Database Hyperscale](/azure/azure-sql/database/service-tier-hyperscale) to better align pricing with use case.
>
>   Consider [elastic pools](/azure/azure-sql/database/elastic-pool-overview) for managing and scaling multiple databases.
>
> - **Optimize component costs:** Consider [reserved capacity for Azure SQL Database](/azure/azure-sql/database/reserved-capacity-overview) for static workloads that you intend to operate for longer timescales.
>
>   Fine-tune backup storage consumption to avoid charges for excess utilization.
>
> - **Optimize application code costs:** Optimize queries and other operations to reduce resource consumption, minimize execution time, and improve overall performance.
> - **Optimize scaling costs:** Incorporate cost optimization considerations into your database scaling strategies.
>
>   You might be able to reduce costs by scaling down databases during periods of lighter utilization. Examples include seasonal workloads that might have weeks or months of decreased load or workloads that are idle overnight.

### Recommendations

|Recommendation|Benefit|
|--------------|-----------|
|Research the available Azure SQL Database [service tiers](/azure/azure-sql/database/sql-database-paas-overview#service-tiers) and select the best model for each use case based on your capacity planning. | Using the appropriate service tier can help you avoid wasted costs from overprovisioning. |
|Optimize your queries, tables, and databases using [Query Performance Insights](/azure/azure-sql/database/query-performance-insight-use) and [Performance Recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal). | Optimizing these operations can help reduce resource consumption and determine the appropriate Azure SQL Database SKUs to provision to meet your performance and budgetary requirements. |
|Use the recommended strategies discussed in the [Automated backups](/azure/azure-sql/database/automated-backups-overview#backup-storage-consumption) guidance to fine-tune your backup storage consumption.</br></br>For vCore databases in Azure SQL Database, the storage consumed by each type of backup (full, differential, and log) is reported on the database monitoring pane as a separate metric. Backup storage consumption up to the maximum data size for a database is included in the price of the database. Excess storage costs depend on the workload and maximum size of the individual databases.|  By understanding the factors that influence backup storage costs, such as database size, rate of change, and retention period, you can optimize your backup strategy to minimize expenses.|
|Consider using the [serverless](/azure/azure-sql/database/serverless-tier-overview) tier for single databases with intermittent, unpredictable usage patterns that can afford some delay in compute warm-up after idle usage periods. | Serverless databases automatically pause and scale their compute resources based on workload demand. Billing is based on the amount of compute used per second. If you have a database with unpredictable or bursty usage patterns interspersed with periods of low or idle usage, serverless is a solution that can help you optimize price-performance.|
|Consider using the [hyperscale](/azure/azure-sql/database/service-tier-hyperscale) tier for all workload types. | The hyperscale tier's cloud-native architecture provides independently scalable compute and storage to support the widest variety of traditional and modern applications. Compute and storage resources in Hyperscale substantially exceed the resources available in the General Purpose and Business Critical tiers. There are no additional SQL licensing costs for Hyperscale databases.|
|[Save costs for resources with reserved capacity](/azure/azure-sql/database/reserved-capacity-overview). Once you've determined the total compute capacity and performance tier for Azure SQL databases in a region, you can use this information to reserve the capacity for one or three years.| You can reduce compute costs associated with Azure SQL Database by prepurchasing compute resources. One year reservations can save you up to 40% of database costs and 3 year reservations can save you up to 60%.|
|Save on SQL licensing costs by exchanging your Software Assurance-enabled SQL Server licenses with [Azure Hybrid Benefit](/azure/azure-sql/azure-hybrid-benefit).| Azure Hybrid Benefit can save you up to 30% on licensing costs in Azure SQL Database and your existing Software Assurance agreement won't be wasted as you migrate databases to Azure. |
|Use [elastic pools](/azure/azure-sql/database/elastic-pool-overview) to help manage and scale multiple databases. | Azure SQL Database elastic pools are a simple, cost-effective solution for managing and scaling multiple databases that have varying and unpredictable usage demands. The databases in an elastic pool are on a single server and share a set number of resources at a set price. |

## Operational Excellence

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure SQL Database.

> [!div class="checklist"]
>
> - **Familiarize yourself with the [Monitoring and performance tuning in Azure SQL Database](/azure/azure-sql/database/monitor-tune-overview) article:** This article contains detailed monitoring guidance, including recommendations for monitoring query performance, configuring alerts, and using automatic tuning to enhance efficiency.
> - **Use infrastructure as code (IaC) technologies:** Use IaC technologies like [Bicep and Azure Resource Manager templates](/azure/azure-sql/database/single-database-create-bicep-quickstart) to deploy Azure SQL Databases to achieve consistent results with reusability in mind.
>
>   Always use the latest version of the Azure Resource Manager API to take advantage of the newest features, security updates, and performance improvements.
>
> - **Monitor your Azure SQL Database:** to detect reliability incidents and optimize performance. Start by monitoring the CPU and IO resources used by your workload. Review the Well-Architected [Health modeling for workloads](/azure/well-architected/design-guides/health-modeling) guide for help designing the reliability and health monitoring solution for your workload.
> - **Optimize management of business continuity and disaster recovery:** Use Azure Backup to protect Azure SQL Database and regularly test your backup strategy.
> - **Use native database administration features:** By adopting Azure SQL Database, DBAs are relieved of many of their traditional tasks, like infrastructure-related administration, backup management, HADR operations, and many others. Encourage thier growth in cloud-native administration and integration with DevOps teams as they adopt a data-as-code mindset.

### Recommendations

|Recommendation|Benefit|
|--------------|-----------|
|[Monitor your Azure SQL Database](/azure/azure-sql/database/monitor-tune-overview) with tools such as Database watcher to detect reliability incidents.|Being able to quickly detect reliability incidents enables you to promptly identify and address any performance issues, minimizing disruptions to your workload.|
|Utilize [continuous integration and continuous deployment (CI/CD) workflows](/sql/tools/sql-database-projects/tutorials/create-deploy-sql-project) to streamline the deployment process of SQL projects. | Maintaining a repeatable workflow for releasing changes minimizes the need for human intervention and significantly reduces the possibility of errors, ensuring a more reliable and efficient deployment process.|
|Use [Azure Backup](/azure/backup/backup-overview) to backup an Azure SQL Database server. |Azure Backup enables centralized management of business continuity and disaster recovery allowing you to efficiently oversee your backup operations. Azure Backup supports long-term retention of recovery points, ensuring your data is preserved for extended periods. Additionally, it provides the capability to perform cross-region and cross-subscription restores, enhancing the flexibility and resilience of your backup strategy.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Azure SQL Database.

> [!div class="checklist"]
>
> - **Familiarize yourself with the [Performance Center for SQL Server Database Engine and Azure SQL Database](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database) article:** This article provides insights into improving the performance of your Azure SQL Database like recommendations for optimizing server configuration and query performance.
>
> - **Select the right tier, features, and billing model:** Microsoft recommends the latest [vCore-based](/azure/azure-sql/database/service-tiers-sql-database-vcore) purchasing model.
>
>    Review resource limits. For specific resource limits per pricing tier (also known as service objective) for single databases, refer to [vCore-based single database resource limits](/azure/azure-sql/database/resource-limits-vcore-single-databases). For elastic pool resource limits, refer to [vCore-based elastic pool resource limits](/azure/azure-sql/database/resource-limits-vcore-elastic-pools).
>
>   Review the [default max degree of parallelism (MAXDOP)](/azure/azure-sql/database/configure-max-degree-of-parallelism) and configure as needed based on a migrated or expected workload.
>
> - **Optimize workload design and application code for performance:** Consider offloading read-only actions to [read-only replicas](/azure/azure-sql/database/read-scale-out).
>
>   Applications connecting to Azure SQL Database should use the latest connection providers, for example the latest [OLE DB Driver](/sql/connect/oledb/oledb-driver-for-sql-server) or [ODBC Driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).
>
>   When using elastic pools, familiarize yourself with [resource governance](/azure/azure-sql/database/elastic-pool-resource-management).

### Recommendations

| Recommendation|Benefit|
|--------|----|
| Review [Performance Recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal). | Database performance can be improved by reviewing and applying performance recommendations.|
| Configure [Automatic tuning](/azure/azure-sql/database/automatic-tuning-overview). | Peak performance and stable workloads can be achieved through continuous performance tuning based on AI and machine learning.|
| Leverage the [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).| The Query Store simplifies troubleshooting query performance by continuously collecting detailed information about all queries. This information allows you to quickly diagnose and address query performance problems.|
| [Tune applications and databases for performance.](/azure/azure-sql/database/performance-guidance) | Properly tuned applications and databases lead to more efficient operations and potentially lower costs.|
| Review [Query Performance Insight](/azure/azure-sql/database/query-performance-insight-use). | Query Performance Insight helps you spend less time troubleshooting database performance by providing deeper insight into database queries by metrics such as CPU, duration, and execution count.|
| [Diagnose and troubleshoot high CPU utilization](/azure/azure-sql/database/high-cpu-diagnose-troubleshoot). | By identifying and addressing the root causes of high CPU usage, you can optimize workload performance and ensure that your database operates efficiently.|
| Understand [blocking](/azure/azure-sql/database/understand-resolve-blocking) and [deadlocking](/azure/azure-sql/database/analyze-prevent-deadlocks) issues. | Resolving blocking and deadlocking problems helps your database operate more efficiently, eliminating bottlenecks and improving performance.|
| Review Azure portal utilization reporting and scale [single databases](/azure/azure-sql/database/single-database-scale) or [elastic pools](/azure/azure-sql/database/elastic-pool-scale) as appropriate. | By regularly reviewing utilization reports, you can ensure your resources are being used efficiently. This practice helps to identify underutilized resources, which can lead to cost savings.|
| Evaluate potential use of [in-memory database objects](/azure/azure-sql/in-memory-oltp-overview).| In-memory technologies enable you to improve the performance of your workload, and potentially reduce database costs.|

## Azure policies

Azure provides an extensive set of built-in policies related to Azure SQL Database. A set of Azure policies can audit some of the preceding recommendations. For example, you can check whether:

- Microsoft Entra-only authentication is enabled upon creation. 
- Consider enabling Zone redundancy to enhance availability and resilience where necessary.
- Long-term geo-redundant backup should be enabled for Azure SQL Databases.
- Setting minimal TLS version to 1.2 improves security by ensuring your SQL Database can only be accessed from clients using TLS 1.2. Using versions of TLS less than 1.2 is not recommended since they have well-documented security vulnerabilities.
- To ensure the operations performed against your SQL assets are captured, SQL servers should have auditing enabled. 

For comprehensive governance, review the built-in definitions for Azure SQL policies that are listed in [Azure Policy built-in definitions](/azure/azure-sql/database/policy-reference).

## Related resources

- Review [Tutorial: Secure a database in Azure SQL Database](/azure/azure-sql/database/secure-database-tutorial).
- For information about supported features, see [Features](/azure/azure-sql/database/features-comparison) and [Resolving Transact-SQL differences during migration to SQL Database](/azure/azure-sql/database/transact-sql-tsql-differences-sql-server).
- Migrating to Azure SQL Database? Review our [Azure Database Migration Guides](/data-migration/).
- Watch episodes of [Data Exposed](/shows/data-exposed/) covering Azure SQL topics and more.
