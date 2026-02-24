---
title: Architecture Best Practices for Azure Database for MySQL
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Database for MySQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: concept-article
ms.date: 02/23/2026
ms.subservice: waf-service-guide
products:
 - azure-database-mysql
azure.category:
 - databases
---

<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD025 -->

# Architecture best practices for Azure Database for MySQL

Azure Database for MySQL - Flexible Server is a fully managed, production-ready relational database service based on the MySQL Community Edition. It provides zone-redundant and local-redundant high availability (HA), managed maintenance windows, automatic backups with point-in-time restore, and elastic scaling across three compute tiers.

This article assumes that as an architect, you've reviewed the [data options decision tree](/azure/architecture/guide/technology-choices/data-options) and chose Azure Database for MySQL as the database for your workload.

The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- markdownlint-disable-next-line MD036 -->
**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Database for MySQL

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>    
>
> - **Review known problems and service limitations that can affect reliability:** Azure Database for MySQL Flexible Server has documented [limitations](/azure/mysql/flexible-server/concepts-limitations) that directly affect reliability design decisions. Review these constraints before provisioning to avoid costly server recreation. Confirm zone-redundant HA requirements and network connectivity method at creation time since these settings can't change later. Account for global transaction identifier (GTID) mode turning on automatically when planning HA configuration.
>
>    Plan initial [storage allocation](/azure/mysql/flexible-server/concepts-service-tiers-storage) carefully since provisioned storage can only scale up and can't be reduced. Avoid using read replicas if you need stop/start functionality, and budget for dump and restore procedures when planning major version upgrades. Select General Purpose or Memory Optimized tiers if your workload requires HA or read replicas.
>
> - **Anticipate potential failure modes and plan mitigation strategies:** Run failure mode analysis to identify potential database failure scenarios and pair each with a targeted mitigation and recovery approach.
>
>   |Failure|Mitigation|
>   |---|---|
>   |Database server hardware fault or process crash causes service interruption|Turn on zone-redundant HA for automatic failover to standby with zero recovery point objective (RPO). Set up connection retry logic in the data access layer.|
>   |Complete availability zone failure disrupts database services in the affected zone|Deploy with zone-redundant HA to distribute primary and standby across zones for automatic failover.|
>   |Accidental data deletion or incorrect updates corrupt or destroy data|Use point-in-time restore to recover to any second within the backup retention period. Deleted servers are recoverable within five days.|
>
> - **Implement redundancy through high availability configurations:** Enable high availability configuration to distribute database components across fault domains and eliminate single points of failure. Deploy zone-redundant HA to place primary and standby servers in different availability zones with zone-redundant storage, which provides automatic failover within 60-120 seconds and zero RPO for committed transactions.
>
>    Consider same-zone HA when your workload accepts zone-level risk or prioritizes cost optimization with locally redundant storage (LRS). This option protects against server-level failures but not zone outages.
>
>    Select zone-redundant HA mode during server provisioning since this setting can't change afterward. Verify GTID mode compatibility with your workload before enabling HA configuration, as GTID automatically turns on and restricts certain SQL operations.
>
> - **Design scaling strategies that maintain reliability during demand changes:** Plan scaling operations to minimize availability impact during demand changes. Schedule compute tier changes during low-traffic periods to manage the brief restart as the workload migrates to a new instance. Use HA-enabled servers to reduce primary downtime, as they perform scaling on the standby first. Rightsize initial storage allocations carefully since capacity can only increase after provisioning.
>
>    Use read replicas to distribute read workloads across up to 10 instances by using asynchronous binary log replication. Select General Purpose or Memory Optimized tiers for servers that need replicas, and monitor replication lag as it varies based on write volume on the primary.
>
> - **Monitor reliability health indicators and set up failure detection alerts:** Set up reliability-focused monitoring to detect service health degradation, replication problems, and resource saturation before they cause application failures. Track HA IO Status, HA SQL Status, and HA Replication Lag to verify standby synchronization health.
>
>    Monitor resource saturation indicators like CPU Percent, Memory Percent, Storage Percent, and IO Percent to reveal capacity pressure. Track Active Connections relative to max_connections to prevent connection exhaustion, and monitor Aborted Connections to detect connectivity problems.
>
>    Set alert thresholds on HA replication lag, storage utilization approaching capacity, and sustained CPU or memory above 80-90 percent to trigger scaling evaluation before availability degrades.
>
> - **Apply configuration and self-preservation techniques to protect against failures:** Database configuration choices directly affect failover reliability. Add explicit primary keys to all tables to improve replication performance and reduce failover duration. Avoid relying on MySQL 8.0's auto-generated invisible primary keys for tables migrated from older versions, as these might be missing.
>
>    Set up connection retry logic in the data access layer to handle brief disruptions during failover, maintenance, and scaling events. Account for DNS propagation delay after failover by configuring applications to retry dropped connections. Apply Azure resource locks and Azure Policy to protect against configuration drift.
>
> - **Design disaster recovery strategy with backup and geo-redundancy capabilities:** Set up disaster recovery with backup, geo-redundancy, and cross-region recovery to meet RPO and recovery time objective (RTO) requirements. Use automated backups that include daily snapshots and transaction log backups every five minutes, with retention configurable from 1 to 35 days.
>
>    Enable geo-redundant backup storage to replicate data to a paired region for regional outage protection. Use geo-restore to create a new server through Universal Geo-restore, or deploy cross-region read replicas as an alternative DR mechanism with manual promotion. Avoid custom port configurations if you need geo-restore or geo-replica capabilities.
>
>    Adjust backup frequency to 6-, 12-, or 24-hour intervals for faster recovery of large databases. Select the appropriate redundancy level, noting that locally redundant storage is the default for non-HA and same-zone HA, but zone-redundant storage is used for zone-redundant HA. Enable geo-redundant backup if you need customer data replicated outside the deployed region.
>

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Turn on [zone-redundant HA](/azure/mysql/flexible-server/concepts-high-availability#zone-redundant-high-availability-ha-architecture) at server creation and specify preferred availability zones for primary and standby placement. Verify GTID mode compatibility with the workload and confirm the target region supports multiple availability zones before provisioning. | Cross-zone fault isolation protects against single availability zone failures through automatic failover with 60-120 second RTO and zero data loss for committed transactions. |
| Turn on [storage autogrow](/azure/mysql/flexible-server/concepts-service-tiers-storage#storage-autogrow) to automatically increase capacity when free storage drops below the threshold. Monitor storage usage trends and account for the permanent nature of each increase. | Prevents the server from entering read-only mode when storage capacity is exhausted and automates capacity management without manual intervention. |
| Create [read replicas](/azure/mysql/flexible-server/concepts-read-replicas) in the same region or across regions to offload read-heavy queries and reporting workloads from the primary.<br><br>Use General Purpose or Memory Optimized tiers for both primary and replica servers. Add explicit primary keys on all tables to improve replication performance. | Offloading read queries from the primary reduces contention and improves write performance.<br><br>Cross-region replicas provide low-latency reads for geographically distributed users. |
| Create [Azure Monitor alerts](/azure/mysql/flexible-server/concepts-monitor-mysql-reference#metrics) on HA IO Status and HA SQL Status to detect standby synchronization failures. Set Replication Lag thresholds aligned with workload RPO requirements.<br><br>Set CPU and Memory alerts at sustained utilization levels and monitor Storage Percent before autogrow triggers. Route critical reliability alerts through Azure Monitor action groups.<br><br>Turn on resource health alerts for platform-initiated availability events and use Azure Monitor workbooks for trend visualization. | Early detection of standby synchronization problems prevents unexpected failover failures. Proactive resource saturation alerts support scaling actions before availability is impacted. |
| Add explicit primary keys to all InnoDB tables, including tables migrated from older MySQL versions. Verify that generated invisible primary key (GIPK) is enabled and avoid multi-table DELETE with CASCADE on [HA-enabled servers](/azure/mysql/flexible-server/concepts-high-availability) to prevent MySQL Bug 102586.<br><br>Audit existing tables for missing primary keys before turning on HA or creating read replicas. Test replication behavior with the workload schema to confirm binary log replay performs within acceptable failover time. | Explicit primary keys reduce failover duration by supporting efficient binary log replay and prevent HA standby desynchronization from MySQL Bug 102586.<br><br>Replication performance improves across read replicas, reducing lag caused by row-based position lookups. |
| Set up automatic connection retry with exponential backoff for [failover](/azure/mysql/flexible-server/concepts-high-availability) disruptions. Set up retry logic for DNS propagation delays after HA failover and test forced failover to validate recovery. Log retry events to track transient fault frequency. | Automatic retry handles transient connection drops during failover, maintenance, and scaling without user impact. This approach supports near-zero-downtime maintenance by gracefully handling brief server restarts during patching. |
| Turn on [geo-redundant backup](/azure/mysql/flexible-server/concepts-backup-restore) storage at server creation to replicate backup data to the Azure paired region. Use Universal Geo-restore for non-paired regions and confirm the server doesn't use a custom port, as geo-restore is unsupported with custom ports.<br><br>Document geo-restore steps and test the process to measure actual RTO. Account for up to one hour of RPO and plan for post-restore tasks including HA reactivation, firewall rule configuration, and DNS updates. | Cross-region backup replication provides protection against entire region failures with 16 nines of backup data durability.<br><br>Universal Geo-restore supports recovery in paired or non-paired regions when the primary region is unavailable. |
| Set [backup retention](/azure/mysql/flexible-server/concepts-backup-restore) between 1 and 35 days based on recovery point requirements. Reduce backup frequency to 6-hour intervals for large databases to speed up point-in-time restore. Use on-demand backups before major schema changes or maintenance operations. | Optimized retention aligns backup coverage with recovery requirements, and configurable backup frequency reduces restore time for large databases by minimizing transaction log replay. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Database for MySQL.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Review the security baseline for Azure Database for MySQL:** The [security baseline](/security/benchmark/azure/baselines/azure-database-for-mysql-flexible-server-security-baseline) maps MySQL Flexible Server capabilities to Microsoft cloud security benchmark controls across network security, identity management, privileged access, data protection, logging, and backup. Review this baseline to understand available security controls and implementation approaches.
>
>    Use Azure Policy built-in definitions to enforce key controls including private endpoint usage, SSL enforcement, customer-managed key (CMK) encryption, and Microsoft Entra authentication. Apply these policies to validate configuration compliance across server instances.
>
>    Account for several baseline expectations that have known gaps. Plan alternative approaches for data leakage prevention and sensitive data classification, as these aren't natively supported. Use MySQL native privileges for data plane access since Azure role-based access control (RBAC) doesn't extend to this layer. Accept platform-managed TLS certificates without Key Vault integration.
>
> - **Segment access through role-based controls:** Access control operates across two distinct planes. Use Azure RBAC to govern management operations like server configuration, firewall rules, and scaling. Apply MySQL native privileges to control data plane operations, as Azure RBAC for data plane access isn't supported.
>
>    Follow least privilege principles for database user accounts. Grant minimum necessary privileges by using GRANT statements scoped to specific databases and tables, create separate accounts for each application component, and avoid assigning ALL PRIVILEGES or SUPER to application accounts.
>
>    Limit Microsoft Entra admin configuration to one per server instance, and reserve the server admin account for emergency access only. Use Customer Lockbox to control Microsoft support access during support incidents.
>
> - **Centralize identity management with Microsoft Entra ID:** Microsoft Entra-only authentication eliminates password-based access entirely. Set up applications to authenticate with short-lived tokens through managed identities or service principals. Assign a user-assigned managed identity and designate a Microsoft Entra administrator for each server instance, noting that only one administrator is allowed per server.
>
>    Use combined MySQL and Microsoft Entra authentication to support gradual migration from password-based to token-based access. Plan a transition timeline to Microsoft Entra-only mode after migrating all connections, as MySQL-only authentication lacks centralized identity governance.
>
> - **Restrict network exposure with connectivity controls:** Use virtual network integration to embed the server into a customer-managed virtual network with a private IP in the delegated subnet, eliminating public internet exposure. Set up the subnet with Microsoft.DBforMySQL/flexibleServers delegation before deployment and apply network security group (NSG) rules to control traffic flow.
>
>    Add private endpoints to create a private IP address in the consumer virtual network that routes traffic over the Azure backbone network. Apply private endpoints to public access servers to provide private connectivity without redeployment, then turn off public network access.
>
>    Limit public access mode to development and migration scenarios only. Plan migration to private connectivity through private endpoints or server redeployment with virtual network integration, as connectivity mode can't change after server creation.
>
> - **Encrypt data at rest and in transit:** Data at rest encryption uses platform-managed keys by default for all stored data, backups, and transaction logs. Use customer-managed keys with Azure Key Vault or Managed HSM to gain additional key life cycle control, which requires a user-assigned managed identity and a Key Vault with soft delete, 90-day retention, and purge protection.
>
>    Enforce TLS 1.2 by default, or use TLS 1.3 on MySQL 8.0 and later for stronger security. Set the require_secure_transport parameter to control TLS enforcement, noting that Federal Information Processing Standards (FIPS)-compliant cipher suites apply automatically. Accept that mutual TLS and custom server certificates aren't supported.
>
>    Trust only root certificate authorities (CAs) in client configurations to avoid connection failures. Azure manages server certificates through DigiCert Global Root G2 and Microsoft RSA Root CA 2017 root CAs, with intermediate CA rotations occurring routinely without announcement.
>
> - **Harden server configuration and reduce attack surface:** Harden server configuration by adjusting parameter-level security settings and reducing attack surface. Set up TLS version enforcement through encryption controls, and accept that cipher suite customization isn't supported as FIPS-compliant suites are enforced automatically.
>
>    Review and set server parameters like local_infile, log_bin_trust_function_creators, and validate_password to strengthen your security posture. Assign custom ports in the 25001-26000 range during server creation only if needed, noting that geo-restore and geo-replica operations don't support custom port configurations.
>
> - **Protect connection credentials and application secrets:** Use identity-based authentication through Microsoft Entra ID with managed identities to replace static passwords in application configurations. Set up token-based authentication to provide short-lived credentials that reduce the exposure window, and set connection strings by using Microsoft Entra authentication to eliminate embedded passwords.
>
>    Store remaining MySQL native authentication passwords, including the server admin password from provisioning, in Azure Key Vault with restricted access. Retrieve secrets independently from applications, as Key Vault native integration for storing MySQL service credentials isn't supported.
>
>    Monitor Key Vault access closely when using CMK encryption, as loss of access makes the server inaccessible within 10 minutes. Create alerts for Key Vault access failures or key deletion events, and maintain key backups with documented recovery procedures.
>
> - **Turn on security monitoring and audit logging:** Audit logs track connection events, administrative operations, data definition language (DDL) and data manipulation language (DML) statements, and table access. Turn on the audit_log_enabled server parameter and select event types based on your security requirements.
>
>    Route audit logs to Azure Monitor through diagnostic settings for Log Analytics, Event Hubs, or Storage destinations. Use the built-in Auditing workbook for security event visualization and create alerts for unusual connection patterns and failed authentication attempts.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create dedicated [database user accounts](/azure/mysql/flexible-server/concepts-azure-ad-authentication) for each application component, by using GRANT statements to assign only the privileges necessary for specific databases and tables.<br><br>Define separate accounts for read-heavy web-tier queries, write-heavy background processing, and administrative tasks. Review and audit privilege assignments regularly, remove unused accounts, and limit server admin usage to break-glass procedures. | Limits the blast radius of compromised credentials to a specific database or table scope and prevents unauthorized privilege escalation between application tiers.<br><br>Supports compliance requirements for separation of duties and least privilege access across database environments. |
| Set up [Microsoft Entra-only authentication](/azure/mysql/flexible-server/concepts-azure-ad-authentication) with a user-assigned managed identity and designated Microsoft Entra administrator for user provisioning. Migrate existing connections to token-based authentication and update client libraries before turning off MySQL auth. | Eliminates password-based authentication vulnerabilities and credential management overhead and centralizes identity governance through Microsoft Entra ID conditional access policies and multifactor authentication.<br><br>Enforces consistent authentication policy across both application and administrative access paths. |
| Deploy servers with [virtual network integration](/azure/mysql/flexible-server/how-to-manage-virtual-network-portal) or private endpoints for production workloads. Choose virtual network integration when the server must reside within a specific virtual network subnet.<br><br>Plan connectivity mode before server deployment because it can't change after provisioning. Verify the delegated subnet has sufficient IP address space for the server and potential replicas, and set up the Microsoft.DBforMySQL/flexibleServers delegation. | Eliminates public internet exposure for database traffic, keeps data within the Azure backbone and customer-controlled network boundaries, and satisfies private connectivity compliance requirements. |
| Set [tls_version to TLS 1.3](/azure/mysql/flexible-server/security-tls) on MySQL 8.0 servers and turn on require_secure_transport to prevent unencrypted connections. Verify all client applications and drivers support TLS 1.3 before enforcing the change in production. | Provides the strongest encryption protocol with improved performance over TLS 1.2, prevents unencrypted connections that expose data to interception, and aligns with industry standards for data-in-transit protection. |
| Create an Azure Key Vault or Managed HSM in the same region as the server with soft delete and purge protection enabled, set retention to 90 days, and generate an RSA 2048, 3072, or 4096 asymmetric key. Review the [customer-managed key requirements](/azure/mysql/flexible-server/concepts-customer-managed-key) for complete prerequisites.<br><br>Assign the server's user-assigned managed identity the Key Vault Crypto Service Encryption User role. [Configure CMK encryption](/azure/mysql/flexible-server/security-how-to-data-encryption-portal#configure-customer-managed-key) through the portal, set a resource lock on the Key Vault to prevent accidental deletion, and monitor key access through audit logs. | Provides full control over the encryption key life cycle including creation, rotation, and revocation, and supports separation of duties between key management and database administration personnel.<br><br>Meets regulatory compliance requirements that mandate customer-controlled encryption keys. |
| Turn off local_infile to prevent client-side file loading into the database.<br><br>Review and restrict log_bin_trust_function_creators, validate_password, and other [parameters that affect security posture](/azure/mysql/flexible-server/security-tls). Document the approved parameter configuration as a security baseline.<br><br>Audit parameter changes through Azure Activity Log and use Azure Policy to enforce necessary configurations across server instances. | Reduces attack surface by turning off unnecessary features, prevents configuration drift that introduces vulnerabilities, and establishes a consistent security posture through policy-enforced configurations. |
| Store the server admin password in an [Azure Key Vault secret](/azure/key-vault/secrets/quick-create-portal) and restrict access by using Azure RBAC with narrow role assignments for break-glass scenarios only. Turn on Key Vault audit logging to track all access to stored credentials.<br><br>Transition application connections to Microsoft Entra ID token-based authentication with managed identities. Remove hardcoded passwords from application code, configuration files, and environment variables, and audit continuous integration and continuous delivery (CI/CD) pipelines for embedded credential references. | Centralizes credential storage in a secured, audited vault instead of distributed configuration files and reduces the risk of credential exposure through source code repositories or deployment artifacts.<br><br>Creates an auditable access trail for all database credential retrievals. |
| Set audit_log_enabled to ON and select event types including CONNECTION_V2 for authentication tracking, DML for data access, and DDL for schema changes. Route [MySqlAuditLogs](/azure/mysql/flexible-server/concepts-monitor-mysql) to Log Analytics through diagnostic settings.<br><br>Create alerts for unusual connection patterns, failed authentication attempts, and administrative operations. Use the built-in Auditing workbook for security event visualization and run Kusto queries to investigate specific events. | Provides an auditable record of database access and administrative operations, helps detect unauthorized access attempts, and supports compliance requirements for activity logging and monitoring. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Database for MySQL and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Develop a cost model for database workload expenses:** Azure Database for MySQL Flexible Server costs span multiple billing dimensions including compute tier and vCore count, storage provisioned in gigabytes (GB), input/output operations per second (IOPS) model, backup storage beyond the free allowance, high availability standby replicas, and read replicas with independent billing. Build a cost model that accounts for all these dimensions.
>
>    Compare pay-as-you-go pricing that charges hourly for compute and per GB for storage against reserved instances offering 1-year or 3-year commitments with increasing discounts for longer terms. Account for compute costs varying significantly across tiers, with Burstable being the lowest-cost option and Memory Optimized being the highest.
>
>    Understand that storage is billed per GB per month and can only scale up after provisioning, making initial sizing a long-term cost factor.
>
>    Account for high availability doubling compute cost by deploying a standby replica with dedicated storage and compute. Include independent charges for read replicas at the same rates as the primary server.
>
>    Calculate backup storage costs, which are free up to 100% of provisioned server storage, with geo-redundant backup charged at twice the locally redundant rate. Include Accelerated Logs cost on General Purpose tier, which doubles when HA is enabled, but it's included on Memory Optimized tier.
>
> - **Monitor database cost metrics and spending trends:** Use Azure Monitor metrics to gain visibility into compute utilization, storage consumption, IOPS usage, and backup storage that directly correlate with service costs.
>
>    Monitor CPU percentage and memory usage to identify over-provisioned servers that consistently run below capacity. Review IO utilization metrics to assess whether the current IOPS model aligns with actual consumption patterns, and analyze usage patterns over time to identify candidates for reserved instance purchases or tier changes.
>
>    Track storage usage trends since storage can only scale up and can't be reduced after provisioning. Monitor the Backup Storage Used metric to understand backup costs relative to the free allowance.
>
>    Watch for storage autogrow events that increase costs as the server approaches capacity limits. Apply resource tags to servers for cost allocation across teams, projects, or environments.
>
> - **Optimize server configuration to reduce ongoing costs:** Three compute tiers provide different cost-performance trade-offs: Burstable (B-series, 1-20 vCores) for intermittent workloads, General Purpose (D-series, 2-96 vCores) for balanced workloads, and Memory Optimized (E-series, 2-96 vCores) for memory-intensive workloads. Select the tier that matches your workload pattern to avoid overspending.
>
>    Set backup retention to the minimum period that meets recovery requirements to reduce backup storage beyond the free allowance. Select locally redundant backup storage for workloads that don't require geo-disaster recovery to avoid the doubled cost of geo-redundant storage.
>
>    Provision storage conservatively since storage can only scale up, and turn on autogrow as a safety net rather than over-provisioning initially.
>
>    Compare Ddsv4/Edsv4 and Ddsv6/Edsv6 series pricing for the target region since newer generations might offer better price-performance ratios. Select Ddsv4/Edsv4 hardware generation when purchasing reservations because they automatically apply to v5 and v6 instances in the same tier and region.
>
> - **Establish spending controls for database resources:** Use Azure Policy, RBAC, and cost management to provide governance mechanisms that prevent costly database configurations and unauthorized resource changes.
>
>    Apply RBAC with least-privilege access to limit who can create, resize, or set up high availability on database servers. Require approval workflows for changes that significantly affect costs like turning on HA, adding read replicas, or upgrading compute tiers.
>
>    Watch for storage autogrow events and IOPS autoscale consumption that can cause cost increases without explicit user action. Track read replica creation and configuration changes that add independent billing for compute and storage.
>
> - **Optimize database configurations for each environment:** Production and nonproduction environments have fundamentally different reliability, performance, and cost requirements that should drive database configuration choices. Set up each environment appropriately to optimize costs.
>
>    Select General Purpose or Memory Optimized tier for production based on workload performance requirements, and evaluate whether zone-redundant HA justifies the compute cost doubling. Size read replicas appropriately for their actual read workload rather than mirroring primary server specifications.
>
>    Use the free tier for proof-of-concept and initial development. Reduce backup retention to 1-7 days and select locally redundant backup storage for nonproduction environments.
>
>    Deploy development servers in cost-effective regions when data residency requirements permit.
>
> - **Consolidate database resources to improve cost efficiency:** Azure Database for MySQL Flexible Server supports hosting multiple databases on a single server instance, which distributes fixed infrastructure costs across workloads. Evaluate consolidation candidates based on compatible performance requirements, security isolation needs, and acceptable noisy-neighbor risk to improve resource utilization.
>
>    Consolidate workloads with different peak usage patterns to maximize benefits from sharing server resources. Plan for cumulative storage growth because provisioned storage can only scale up.
>
>    Deploy read replicas for analytical and reporting workloads instead of provisioning separate servers. Size read replica compute independently from the primary server to match actual read workload requirements. Use General Purpose or Memory Optimized tiers since read replicas are unavailable on Burstable tier, and budget for independent billing for compute and storage.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create [budget alerts](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) at multiple thresholds in Microsoft Cost Management for database resource groups and turn on forecast alerts to detect trending overruns before they occur.<br><br>Set up anomaly detection to identify unexpected cost spikes from storage autogrow or IOPS consumption changes. | Detects cost overruns and unexpected spending patterns before they affect the budget. Provides early warning for silent cost increases from autogrow and autoscale IOPS. |
| Rightsize the [compute tier](/azure/mysql/flexible-server/concepts-compute-storage) based on actual workload utilization patterns. Evaluate CPU credit consumption on Burstable tier servers to determine whether the workload requires upgrading to General Purpose.<br><br>Assess whether General Purpose servers with consistently high memory pressure would benefit from Memory Optimized tier. Use compute scaling to adjust vCores when workload demand changes, accepting a server restart of 60-120 seconds during the scaling event. | Eliminates overspending on compute capacity that exceeds actual workload requirements and matches compute costs to workload demand by selecting the most cost-effective tier. |
| Purchase [reserved instances](/azure/mysql/flexible-server/concept-reserved-pricing) for servers with predictable sustained compute usage. Select 1-year or 3-year reservations, with longer commitments yielding greater savings.<br><br>Use vCore size flexibility to apply reservations across different compute sizes within the same performance tier and region. Determine the right reservation size based on total vCore usage across all servers in the target region and tier. | Reduces compute costs compared to pay-as-you-go pricing for stable production workloads and provides budget predictability through fixed upfront or monthly commitment payments. |
| Apply [Azure Policy](/azure/governance/policy/overview) to restrict allowed compute tiers and stock keeping unit (SKU) sizes, preventing accidental provisioning of oversized instances.<br><br>Enforce tagging policies on database servers to make sure cost allocation tags exist for financial tracking and limit deployment regions to prevent cross-region deployments that increase data transfer costs. | Prevents accidental provisioning of high-cost configurations through automated policy enforcement and ensures consistent cost tracking through mandatory resource tagging. |
| Select [Burstable B-series instances](/azure/mysql/flexible-server/concepts-compute-storage) for development and testing workloads that have intermittent CPU demand. Burstable tier doesn't support high availability or read replicas, which aligns with nonproduction tolerance for brief outages.<br><br>Start with the smallest Burstable SKU and scale up only if workload testing requires more resources. | Reduces nonproduction compute costs compared to General Purpose tier and matches compute capacity to intermittent development and testing workload patterns. |
| Use the [stop/start capability](/azure/mysql/flexible-server/how-to-stop-start-server-portal) to halt nonproduction servers outside business hours or between testing cycles. Account for the 30-day maximum stop duration since Azure automatically restarts stopped servers after that period.<br><br>Stopped servers still incur charges for provisioned storage, provisioned IOPS, and backup storage within the retention window. | Eliminates compute charges for servers that sit idle during nights, weekends, or between development sprints and preserves the server configuration and data. |
| Consolidate multiple databases onto shared [Flexible Server instances](/azure/mysql/flexible-server/how-to-create-manage-databases) after evaluating current per-database utilization to identify servers running below capacity.<br><br>A higher-tier server hosting multiple workloads often costs less than running separate lower-tier servers for each database. | Improves resource utilization by sharing compute and storage across multiple databases on fewer servers, reducing total compute cost by eliminating redundant per-server overhead. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Database for MySQL.

> [!div class="checklist"]
>
> - **Define server infrastructure by using code templates:** Use infrastructure as code (IaC) with Azure Resource Manager templates, Bicep, Terraform, or Azure CLI for declarative Flexible Server provisioning.
>
>   - Define HA mode, backup redundancy, network access mode, and MySQL version correctly in the initial deployment template, as these settings are immutable after creation and require server recreation to change.
>
>   - Parameterize compute tier, SKU, storage size, IOPS, and backup retention for environment-specific variations.
>
>   - Deploy networking resources before server creation when using virtual network integration, then set up diagnostic settings and RBAC as post-provisioning layers. 
>
>   - Set up drift detection for parameters modified through the portal outside IaC workflows.
>
> - **Automate database schema deployments in pipelines:** Integrate database schema changes into deployment pipelines by using migration scripts with validation gates and rollback capability. Use tools like Flyway, Liquibase, or custom scripts to version-control schema changes and apply them through CI/CD pipeline stages.
>
>    Include premigration validation to check for GTID-incompatible operations when targeting HA-enabled servers, since statements like CREATE TABLE ... SELECT are unsupported. Test schema changes against nonproduction servers by using Burstable tier to reduce costs and validate compatibility.
>
> - **Set up monitoring and logging:** Set up Azure Monitor integration with metrics, resource logs, diagnostic settings, and workbook templates for operational visibility. Use platform metrics covering CPU, memory, storage, IO, connections, and replication lag at one-minute frequency with 30-day retention.
>
>    Balance log detail level with performance impact since verbose logging affects server throughput. Set up the server logs feature for short-term file-based access alongside diagnostic settings for long-term analytics, and plan log routing to Event Hubs for security information and event management (SIEM) integration or Azure Storage for compliance archival.
>
> - **Define operational procedures for routine and emergency response:** Establish documented procedures covering change management, maintenance coordination, and emergency recovery to ensure consistent operational practices across routine and critical scenarios.
>
>   - Require on-demand backups as a gate before schema migrations, parameter changes, and version upgrades to create rollback points. Track backup consumption against the 50-backup-per-server limit and schedule cleanup of outdated backups.
>
>   - Reschedule maintenance events when they conflict with critical business periods, noting this capability is available only for General Purpose and Memory Optimized tiers. Set up Azure Service Health alerts to receive notifications about maintenance events and service incidents.
>
>   - Maintain a runbook for deleted server recovery, available within five days of deletion, including escalation paths and validation steps. Document post-restore reconfiguration steps since restored servers require HA reactivation, firewall rule updates, and parameter resets for time_zone and event_scheduler.
>
> - **Establish safe deployment practices for server updates:** Coordinate maintenance windows, patching strategies, and parameter changes to minimize disruption while maintaining security and functionality. Balance near-zero-downtime capabilities with proper testing and workload-aware scheduling.
>
>   - Set up Custom Managed Maintenance Window for production workloads to control the day and 60-minute Coordinated Universal Time (UTC) start window, minimizing disruption through managed maintenance with near-zero-downtime patching. Use Virtual Canary policy for nonproduction servers to validate patch compatibility before production rollout.
>
>   - Make sure all tables have primary keys, which the near-zero-downtime patching mechanism requires to minimize downtime to 10-30 seconds. Set up retry logic in the data access layer to handle brief connection interruptions during maintenance, and schedule maintenance during low-workload periods.
>
>   - Distinguish dynamic parameters that apply immediately to new connections from static parameters that require server restart. Account for static parameter changes restarting both primary and standby servers when HA is enabled, and test parameter changes in nonproduction environments.
>
> - **Automate management and monitoring tasks:** Use built-in automation features and Azure Monitor integration to reduce manual operational overhead. Set up automated backups with adjustable intervals and retention periods to maintain recovery point objectives, and review Azure Advisor recommendations periodically for optimization suggestions on performance, reliability, and cost.
>
>    Automate configuration management by using Azure CLI or REST API scripts to apply consistent server parameter configurations across server fleets. Set up automated compliance checks comparing current parameters against baseline configurations to detect drift, and schedule on-demand backups through automation before planned maintenance or change operations.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Include HA mode, backup redundancy, network access mode, MySQL version, and lower_case_table_names as explicit template parameters since these settings can't change after server creation.<br><br>Parameterize compute tier, SKU name, storage size, IOPS, and backup retention for environment-specific deployments.<br><br>Use [Bicep or Azure Resource Manager template modules](/azure/mysql/flexible-server/quickstart-create-bicep) from the MySQL Flexible Server quickstart references as starting points, extending them with diagnostic settings, firewall rules, and RBAC assignments. | Prevents costly server recreation by capturing all immutable settings in the initial deployment template. Environment parameterization supports consistent provisioning across development, staging, and production environments. |
| Turn on the [server logs feature](/azure/mysql/flexible-server/how-to-server-logs-portal) to capture slow query logs and audit logs as downloadable files for short-term operational access. Set the log_output server parameter to FILE so log entries can be listed and downloaded through the portal, CLI, or REST API.<br><br>Set up diagnostic settings alongside server logs to route MySqlSlowLogs and MySqlAuditLogs to a Log Analytics workspace for long-term retention and Kusto Query Language (KQL)-based analysis. | Server logs provide immediate file-based access to slow query and audit log data for rapid troubleshooting without requiring Log Analytics queries.<br><br>Combining server logs with diagnostic settings provides both short-term operational access and long-term analytics retention. |
| Turn on audit_log_enabled in server parameters and select event types like CONNECTION_V2, DML_NONSELECT, DDL, and data control language (DCL) that match compliance requirements.<br><br>Use audit_log_include_users and audit_log_exclude_users to scope logging to application and administrative accounts and exclude service accounts.<br><br>Create a diagnostic setting to send MySqlAuditLogs to a Log Analytics workspace, then use the [Auditing workbook](/azure/mysql/flexible-server/tutorial-configure-audit) to visualize connection events, administrative actions, and table access summaries. | Provides database-level activity tracking for compliance audits covering connection attempts, schema changes, and privilege modifications. The Auditing workbook delivers ready-made visualizations for audit event analysis without requiring custom KQL queries. |
| Require an [on-demand backup](/azure/mysql/flexible-server/concepts-backup-restore) before schema migrations, parameter changes, or version upgrades to create a known-good rollback point.<br><br>Track backup usage against the 50-backup-per-server limit and incorporate cleanup of outdated backups into operational routines.<br><br>Include restore validation in the deployment checklist so teams confirm the backup is usable before proceeding with the change. | Provides an explicit recovery point tied to each change, reducing potential data loss if rollback is needed. This approach embeds backup discipline into the change management workflow rather than relying on automated daily snapshots alone. |
| Add a CanNotDelete management lock to production MySQL Flexible Server resources to prevent accidental deletion through the portal, CLI, or automation. Include lock resources in IaC templates to apply locks automatically during provisioning.<br><br>Document the [deleted server recovery procedure](/azure/mysql/flexible-server/concepts-backup-restore), available within five days of deletion, as a fallback for situations where management locks weren't in place. | Prevents irreversible data loss from accidental deletion of production database servers and their associated data.<br><br>Management locks provide a safety net without affecting normal operational tasks like configuration changes or restarts. |
| Select [Custom Managed Maintenance Window](/azure/mysql/flexible-server/how-to-maintenance-portal) policy and specify the preferred day and 60-minute UTC start time that aligns with lowest workload periods. Assign servers to maintenance Batch 1 or Batch 2 to stagger updates between test and production environments.<br><br>Monitor upcoming maintenance through the portal maintenance status section and reschedule when conflicts arise with planned business events. | Aligns planned maintenance with lowest traffic periods to minimize workload disruption during patching. Staggering batches between environments provides early warning of problems before production receives updates. |
| Automate [server parameter configuration](/azure/mysql/flexible-server/how-to-configure-server-parameters-cli) across server fleets to apply consistent baselines for slow query logging, audit logging, and connection limits.<br><br>Script periodic parameter exports and compare them against a stored baseline configuration to detect drift from portal or unplanned changes.<br><br>Integrate parameter validation into CI/CD pipelines so infrastructure deployments verify that critical server parameters match the expected configuration before completing. | Consistent parameter baselines across environments prevent configuration-related incidents caused by unplanned portal changes.<br><br>Automated drift detection identifies unauthorized or accidental parameter modifications before they affect workload behavior. |
| Turn on the [storage autogrow](/azure/mysql/flexible-server/concepts-service-tiers-storage#storage-autogrow) setting to automatically increase storage capacity when utilization approaches the provisioned limit. Monitor storage percentage metrics alongside autogrow events to forecast storage growth trends and plan capacity adjustments.<br><br>Account for the fact that storage can only scale up and never decrease when planning initial storage allocation and autogrow thresholds. | Prevents the server from entering read-only mode when storage capacity is exhausted, maintaining write availability for database operations. Automatic scaling eliminates the need for manual intervention during storage growth events. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Database for MySQL.

> [!div class="checklist"]
>
> - **Run capacity planning:** Compute, storage, and IOPS dimensions determine the performance ceiling. Run capacity planning to evaluate vCore and memory needs based on query complexity, concurrent connections, and workload type. Account for InnoDB buffer pool memory allocation, which varies by compute size and determines cache efficiency, along with max_connections limits that scale with available memory.
>
>    Estimate storage growth considering that allocation can only scale up and never be reduced. Calculate IOPS requirements based on read/write ratios, transaction volume, and peak demands. Use Storage IO Percent and Storage IO Count metrics to define baseline usage patterns.
>
>    Plan for connection pooling when concurrent application connections approach max_connections limits. Identify read versus write workload ratios to determine whether read replicas provide the horizontal read scale your workload requires, and include failover capacity in your planning.
>
> - **Choose compute tiers and SKUs based on performance requirements:** Azure Database for MySQL offers three compute tiers with distinct performance characteristics and feature availability. Match tier selection to workload performance needs, scalability requirements, and feature dependencies.
>
>   - Select General Purpose tier (D-series) for production workloads requiring sustained compute capacity without throttling. Provides balanced compute and memory with support for HA, read replicas, and accelerated logs. Use for most production scenarios where predictable performance and enterprise features are required.
>
>   - Choose Memory Optimized tier (E-series) when workloads require higher memory-to-vCore ratios for cache-dependent and high-concurrency scenarios. Delivers the lowest latency and highest queries per second (QPS). Best for memory-intensive workloads with large working sets or high-concurrency requirements.
>
>   - Limit Burstable tier (B-series) to development and testing environments because the CPU credit model throttles under sustained load. Doesn't support HA, read replicas, or accelerated logs, which aligns with nonproduction requirements.
>
>   - Account for IOPS limits that scale with compute size, and make sure InnoDB buffer pool allocation matches working set requirements since it varies by SKU. Select General Purpose or Memory Optimized tiers when you need HA, read replicas, or accelerated logs support.
>
> - **Define a scaling strategy:** Plan compute tier changes for low-traffic periods since they require server restarts taking 60 to 120 seconds, causing brief workload disruption. Apply storage increases online, but remember that allocation can never be reduced. Use autoscale IOPS to provide automatic I/O scaling based on demand without manual intervention.
>
>    Account for read replicas using asynchronous binary log replication, which introduces measurable lag between source and replica data. Select General Purpose or Memory Optimized tiers for replica support, and set up application-level routing since each replica has its own connection endpoint. Provision replicas with specifications equal to or exceeding the source to prevent lag accumulation.
>
> - **Monitor database performance metrics:** Monitor CPU Percent, Memory Percent, and Storage IO Percent to identify resource saturation before it affects query performance. InnoDB buffer pool metrics like pages_data, pages_free, and read request ratios reveal cache efficiency and memory pressure.
>
>    Slow_queries count identifies queries exceeding the long_query_time threshold for optimization. InnoDB Row Lock Time and Row Lock Waits reveal lock contention affecting transaction throughput, and lock_deadlocks and lock_timeouts indicate concurrency conflicts requiring query or schema changes.
>
>    Azure Monitor provides one-minute granularity metrics across Availability, Latency, Saturation, and Traffic categories. Slow query logs and the Query Performance Insight workbook offer query-level visibility, and Log Analytics supports historical trend analysis through Kusto queries.
>
> - **Run performance testing:** Create load test workloads that reflect production query patterns, including read/write ratios, transaction complexity, and concurrency levels. Include peak period simulations to validate autoscale IOPS behavior and compute headroom. Test with production-representative data volumes because InnoDB buffer pool efficiency and query plans depend on data size.
>
>    Test compute tier scaling to measure restart duration and application reconnection behavior. Simulate read replica lag under load and verify storage autogrow activation. Capture baseline metrics for query latency, throughput, IOPS consumption, and connection count to compare across IOPS settings and detect cache sizing problems during growth.
>
> - **Optimize database performance through configuration and features:** Tune MySQL engine parameters and Azure-specific features based on workload characteristics to maximize query performance, reduce resource contention, and improve overall database efficiency.
>
>   - InnoDB buffer pool size determines how much working data is cached in memory, directly affecting the cache hit ratio. Set transaction log sizing to balance write throughput against crash recovery time. Select appropriate table storage strategy to affect performance and manageability for large databases.
>
>   - Set up connection pooling at the proxy or application layer to reduce connection creation and teardown overhead under high concurrency. Monitor and close idle connections that consume server thread resources, reducing capacity for active queries.
>
>   - Use accelerated logs to optimize transaction log operations by separating log I/O to high-performance storage. Verify feature availability by compute tier and region, and benchmark performance gains to quantify improvement for specific workloads.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Turn on [autoscale IOPS](/azure/mysql/flexible-server/concepts-storage-iops) for variable or unpredictable I/O patterns to scale dynamically with demand. Use pre-provisioned IOPS for workloads with consistent, predictable throughput.<br><br>Switch between IOPS models at any time through the Compute + storage settings without data loss or service disruption.<br><br>Track Storage IO Percent and Storage IO Count to confirm the model meets workload needs. Sustained values near 100% indicate throttling that requires a model change or tier upgrade. | Autoscale IOPS prevents storage throttling during workload spikes by dynamically adjusting to demand. Pre-provisioned IOPS delivers predictable performance for steady-state workloads with fixed throughput requirements. |
| Offload read queries to [read replicas](/azure/mysql/flexible-server/concepts-read-replicas) to free capacity on the primary server for write operations. [Create replicas](/azure/mysql/flexible-server/how-to-read-replicas-portal) in the same region or cross-region based on latency and recovery needs.<br><br>Use a connection proxy like ProxySQL to distribute read traffic automatically. Provision replicas with equal or greater compute and storage than the source server.<br><br>Track Replication Lag to detect delays and set alerts on lag exceeding acceptable thresholds. Make sure all source tables have primary keys because missing keys increase replication latency. | Offloading read queries from the primary server frees compute and I/O capacity for write operations. Cross-region replicas bring data closer to users, reducing read latency in distributed applications. |
| Plan [compute tier scaling](/azure/mysql/flexible-server/concepts-service-tiers-storage#scale-resources) during low-traffic periods to minimize the impact of the necessary restart. Turn on storage autogrow to prevent the server from entering read-only mode when capacity is exhausted.<br><br>Update replica compute to equal or exceed the source configuration before scaling the source server. Test application reconnection behavior to confirm graceful handling of the 60-to-120-second restart window. | Scheduled scaling reduces the impact of necessary server restarts on active workloads. Storage autogrow prevents the server from entering read-only mode, which would halt write operations. |
| Set slow_query_log to ON, adjust long_query_time from the default 10 seconds to match your baseline, and turn on log_queries_not_using_indexes to catch full table scans. Send [MySqlSlowLogs](/azure/mysql/flexible-server/concepts-monitor-mysql) to a Log Analytics workspace for [Kusto query analysis](/azure/mysql/flexible-server/tutorial-query-performance-insights). | Pinpoints specific queries causing performance degradation through execution time and scan pattern analysis. Historical trend data detects gradual regressions before they affect users. |
| Create [alerts](/azure/mysql/flexible-server/concepts-monitor-mysql-reference#metrics) on CPU Percent, Memory Percent, and Storage IO Percent approaching saturation. Alert on Storage Percent to detect capacity limits before autogrow triggers, and monitor Replication Lag on read replicas.<br><br>Alert on increasing InnoDB Row Lock Waits and lock_deadlocks to detect concurrency bottlenecks. Monitor Slow_queries trends and track Aborted Connections to identify connection problems affecting performance. | Detects resource saturation and engine contention before they cause query timeouts or application failures. Supports proactive response to performance degradation through automated notification of threshold breaches. |
| Turn on [accelerated logs](/azure/mysql/flexible-server/concepts-accelerated-logs) through the Compute + storage settings for Memory Optimized or General Purpose tiers. Plan for a server restart during activation and verify the server has an availability zone allocated.<br><br>Storage autogrow can't be turned off while accelerated logs are active, and binlog_expire_logs_seconds is ignored. Test workload performance before and after activation to quantify throughput and latency gains. | Reduces transaction commit latency by placing transaction logs on high-performance storage. Increases query throughput in high-concurrency scenarios at no additional cost on Memory Optimized tier. |
| Set innodb_buffer_pool_size to the maximum value supported by the compute SKU for the best cache hit ratio. Choose [innodb_log_file_size](/azure/mysql/flexible-server/concepts-server-parameters) based on write volume, accepting longer crash recovery with larger sizes.<br><br>Keep innodb_tmpdir at the default /mnt/temp for SSD-speed temporary sort operations during online ALTER TABLE.<br><br>Turn on innodb_file_per_table for databases with tables approaching the 8 TB system tablespace limit. Adjust sort_buffer_size if Sort_merge_passes indicates excessive merge passes during sorting. | Dedicating available memory to InnoDB buffer pool caching maximizes page cache efficiency. Appropriate transaction log sizing balances write throughput against crash recovery time. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Database for MySQL and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Microsoft Entra-only authentication is enabled on flexible servers.
- Microsoft Entra authentication is configured for centralized identity management.
- Customer-managed keys are used for encryption at rest.

For governance, review the [Azure Policy built-in definitions for Azure Database for MySQL](/azure/governance/policy/samples/built-in-policies#azure-database-for-mysql) and other policies that might affect the security of the database.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [WordPress on Azure App Service](/azure/architecture/example-scenario/infrastructure/wordpress-app-service).

## Related content

- [Azure Database for MySQL Flexible Server documentation](/azure/mysql/flexible-server/)
- [Migrate Azure Database for MySQL Single Server to Flexible Server](/azure/mysql/migrate/migrate-single-flexible-mysql-import-cli)
- Training: [Azure Database for MySQL Flexible Server](/training/modules/implement-azure-database-for-mysql-features-that-support-mission-critical-workloads/)
