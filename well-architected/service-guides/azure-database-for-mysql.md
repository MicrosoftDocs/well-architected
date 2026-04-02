---
title: Architecture Best Practices for Azure Database for MySQL
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Database for MySQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: concept-article
ms.date: 02/27/2026
ms.subservice: waf-service-guide
products:
 - azure-database-mysql
azure.category:
 - databases
---

<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD025 -->

# Architecture best practices for Azure Database for MySQL

Azure Database for MySQL flexible server is a fully managed, production-ready relational database service based on the MySQL Community Edition. It provides zone-redundant and local-redundant high availability (HA), managed maintenance windows, automatic backups with point-in-time restore, and elastic scaling across three compute tiers.

This article assumes that as an architect, you've reviewed the [data store decision tree](/azure/architecture/guide/technology-choices/data-stores-getting-started) and chose Azure Database for MySQL as the database for your workload.

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
> - **Review known problems and service limitations that can affect reliability:** Azure Database for MySQL flexible server has [limitations](/azure/mysql/flexible-server/concepts-limitations) that affect your reliability design. Review these constraints before you provision servers to avoid costly server re-creation. Confirm your zone-redundant HA requirements and network connectivity method at creation because you can't change these settings later.
>
>    Plan initial [storage allocation](/azure/mysql/flexible-server/concepts-service-tiers-storage) carefully because you can only scale storage up and can't reduce it. Avoid using read replicas if you need stop/start functionality, and budget for dump and restore procedures when you plan major version upgrades. Select General Purpose or Memory Optimized tiers if your workload requires HA or read replicas.
>
> - **Anticipate potential failure modes and plan mitigation strategies:** Run failure mode analysis to identify potential database failure scenarios and pair each scenario with a targeted mitigation and recovery approach.
>
>   | Failure | Mitigation |
>   | --- | --- |
>   | A database server hardware fault or process crash causes service interruption. | Turn on zone-redundant HA for automatic failover to the standby, which achieves zero recovery point objective (RPO). Set up connection retry logic in the data access layer. |
>   | A complete availability zone failure disrupts database services in the affected zone. | Deploy a zone-redundant HA configuration to distribute primary and standby servers across zones for automatic failover. |
>   | An accidental data deletion or incorrect update corrupts or destroys data. | Use point-in-time restore to recover to any second within the backup retention period. You can recover deleted servers within five days. |
>
> - **Implement redundancy through HA configurations:** Use a HA configuration to distribute database components across fault domains and eliminate single points of failure. Deploy zone-redundant HA to place primary and standby servers in different availability zones that use zone-redundant storage (ZRS), which provides automatic failover within 60 to 120 seconds and zero RPO for committed transactions.
>
>    Consider same-zone HA when your workload accepts zone-level risk or prioritizes cost optimization by using locally redundant storage (LRS). This option protects against server-level failures but not zone outages.
>
>    Select zone-redundant HA mode when you provision the server because you can't change this setting later. 
>
> - **Design scaling strategies that maintain reliability during demand changes:** Plan scaling operations to minimize availability impact and distribute load across database instances.
>
>    - Schedule compute tier changes during low-traffic periods to manage the brief restart as the workload migrates to a new instance. Use servers that have HA set up to reduce primary downtime because they perform scaling on the standby first. Rightsize initial storage allocations carefully. You can only increase storage capacity after provisioning, not reduce it.
>
>    - Use read replicas to distribute read workloads across up to 10 instances by using asynchronous binary log replication. Select General Purpose or Memory Optimized tiers for servers that need replicas, and monitor replication lag because it varies based on write volume on the primary.
>
> - **Monitor reliability health indicators and set up failure detection alerts:** Set up reliability-focused monitoring to detect service health degradation, replication problems, and resource saturation before they cause application failures. Track HA synchronization status and replication lag to check standby health.
>
>    Monitor resource saturation indicators for compute, memory, storage, and input/output (I/O) to reveal capacity pressure. Track active connections relative to connection limits to prevent exhaustion, and monitor aborted connections to detect connectivity problems.
>
>    Set alert thresholds on replication lag, storage utilization that's approaching capacity, and sustained compute or memory pressure so that you can scale before availability degrades.
>
> - **Apply configuration and self-preservation techniques to protect against failures:** Apply database configuration and platform protection measures to improve failover reliability and prevent configuration drift.
>
>    - Add explicit primary keys to all tables to improve replication performance and reduce failover duration.
>
>    - Set up connection retry logic in the data access layer to handle brief disruptions during failover, maintenance, and scaling events. Account for Domain Name System (DNS) propagation delay after failover by configuring applications to retry dropped connections.
>
>    - Apply Azure resource locks and Azure Policy to protect against configuration drift.
>
> - **Design a disaster recovery (DR) strategy that includes backup and geo-redundancy capabilities:** Define how backup, geo-redundancy, and cross-region recovery are used to meet RPO and recovery time objective (RTO) requirements.
>
>    - Set automated backup frequency and retention periods to achieve your workload's RPO. Adjust backup intervals for faster recovery of large databases.
>
>    - Use geo-redundant backup storage to replicate data to a paired region for regional outage protection. Use geo-restore to create a new server through universal geo-restore, or deploy cross-region read replicas as an alternative DR mechanism that requires manual promotion. Avoid custom port configurations if you need geo-restore or geo-replica capabilities.
>
>   - Select the appropriate redundancy level. Non-HA and same-zone HA deployments default to LRS, while zone-redundant HA deployments use ZRS. Turn on geo-redundant backup to replicate customer data outside the deployed region.

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Turn on [zone-redundant HA](/azure/mysql/flexible-server/concepts-high-availability#zone-redundant-high-availability-ha-architecture) when you create the server and choose your preferred availability zones for the primary server and standby servers. Before you create the server, make sure the workload supports Global Transaction Identifier (GTID) mode and that the target region supports multiple availability zones. | Cross-zone fault isolation protects against single availability zone failures through automatic failover and achieves 60-120 second RTO and zero data loss for committed transactions. |
| Turn on [storage autogrow](/azure/mysql/flexible-server/concepts-service-tiers-storage#storage-autogrow) to automatically increase capacity when free storage drops below the threshold. Monitor storage usage trends and remember that each increase is permanent. | Prevents the server from entering read-only mode when storage capacity runs out and automates capacity management without manual steps. |
| Create [read replicas](/azure/mysql/flexible-server/concepts-read-replicas) in the same region or across regions to offload read-heavy queries and reporting workloads from the primary. <br><br> Use General Purpose or Memory Optimized tiers for the primary server and replica servers. Add explicit primary keys on all tables to improve replication performance. | Offloading read queries from the primary reduces contention and improves write performance. <br><br> Cross-region replicas provide low-latency reads for geographically distributed users. |
| Create [Azure Monitor alerts](/azure/mysql/flexible-server/concepts-monitor-mysql-reference#metrics) on *HA IO Status* and *HA SQL Status* to detect standby synchronization failures. Set *Replication Lag* thresholds that align with workload RPO requirements. <br><br> Set CPU and memory alerts at sustained utilization levels and monitor the storage percent before autogrow triggers. Route critical reliability alerts through Azure Monitor action groups. <br><br> Turn on resource health alerts for platform-initiated availability events and use Azure Monitor workbooks for trend visualization. | Early detection of standby synchronization problems prevents unexpected failover failures. Proactive resource saturation alerts support scaling actions before resource saturation affects availability. |
| Add explicit primary keys to all InnoDB tables, including tables that you migrate from older MySQL versions. Turn on the generated invisible primary key (GIPK) setting. On [HA-enabled servers](/azure/mysql/flexible-server/concepts-high-availability), avoid multitable `DELETE` statements with `CASCADE` to prevent MySQL Bug 102586. <br><br> Check existing tables for missing primary keys before you turn on HA or create read replicas. Test replication behavior with the workload schema to make sure that binary log replay works within the acceptable failover time. | Explicit primary keys reduce failover duration by supporting efficient binary log replay and prevent HA standby desynchronization from MySQL Bug 102586. <br><br> Replication performance improves across read replicas, which reduces lag caused by row-based position lookups. |
| Set up automatic connection retry that uses exponential backoff for [failover](/azure/mysql/flexible-server/concepts-high-availability) disruptions. Set up retry logic for DNS propagation delays after HA failover and test forced failover to check recovery. Log retry events to track transient fault frequency. | Automatic retry handles transient connection drops during failover, maintenance, and scaling without user impact. This approach supports near-zero-downtime maintenance by gracefully handling brief server restarts during patching. |
| Turn on [geo-redundant backup](/azure/mysql/flexible-server/concepts-backup-restore) storage when you create the server to replicate backup data to the Azure paired region. Use universal geo-restore for nonpaired regions. Confirm that the server doesn't use a custom port because they don't support geo-restore. <br><br> Document geo-restore steps and test the process to measure actual RTO. Account for up to one hour of RPO and plan for post-restore tasks, including HA reactivation, firewall rule configuration, and DNS updates. | Cross-region backup replication provides protection against entire region failures and delivers 99.9999999999999999% (16 nines) backup data durability. <br><br> Universal geo-restore supports recovery in paired or nonpaired regions when the primary region becomes unavailable. |
| Set [backup retention](/azure/mysql/flexible-server/concepts-backup-restore) between 1 and 35 days based on recovery point requirements. Reduce backup frequency to 6-hour intervals for large databases to speed up point-in-time restore. Use on-demand backups before major schema changes or maintenance operations. | Optimized retention aligns backup coverage with recovery requirements, and configurable backup frequency reduces restore time for large databases by minimizing transaction log replay. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Database for MySQL.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baseline for Azure Database for MySQL:** The [security baseline](/security/benchmark/azure/baselines/azure-database-for-mysql-flexible-server-security-baseline) maps MySQL flexible server capabilities to Microsoft cloud security benchmark controls across network security, identity management, privileged access, data protection, logging, and backup. Review this baseline to understand available security controls and implementation approaches.
>
>    Use Azure Policy built-in definitions to enforce key controls including private endpoint usage, Secure Sockets Layer (SSL) enforcement, customer-managed key encryption, and Microsoft Entra authentication. Apply these policies to validate configuration compliance across server instances.
>
> - **Segment access through role-based access control (RBAC):** Set up layered access control across management and data planes to enforce least privilege access and restrict admin access.
>
>    - Use Azure RBAC to govern management operations and MySQL native privileges to control data plane operations. Flexible server doesn't support Azure RBAC for data plane access.
>
>    - Follow least privilege principles for database user accounts. Grant minimum necessary privileges for specific databases and tables, create separate accounts for each application component, and avoid broad privilege assignments.
>
>    - Limit Microsoft Entra admin configuration to one per server instance, and reserve the server admin account for emergency access only. Use Customer Lockbox to control Microsoft support access during support incidents.
>
> - **Centralize identity management through Microsoft Entra ID:** Microsoft Entra-only authentication eliminates password-based access entirely. Set up applications to authenticate by using short-lived tokens through managed identities or service principals. Assign a user-assigned managed identity and designate a Microsoft Entra admin for each server instance. Each server supports only one admin.
>
>    Use combined MySQL and Microsoft Entra authentication to support gradual migration from password-based to token-based access. Plan a transition timeline to Microsoft Entra-only mode after you migrate all connections because MySQL-only authentication lacks centralized identity governance.
>
> - **Restrict network exposure by using connectivity controls:** Use virtual network integration to embed the server into a customer-managed virtual network that has a private IP address in the delegated subnet, which eliminates public internet exposure. Set up the required subnet delegation before deployment and apply network security group (NSG) rules to control traffic flow.
>
>    Limit public access mode to development and migration scenarios only. Plan migration to private connectivity through private endpoints or server redeployment that uses virtual network integration. The connectivity mode can't change after server creation.
>
> - **Encrypt data at rest and in transit:** Protect stored data and network traffic through encryption mechanisms for both at-rest and in-transit scenarios.
>
>    - Data-at-rest encryption uses platform-managed keys by default for all stored data, backups, and transaction logs. Use customer-managed keys through Azure Key Vault or Azure Key Vault Managed HSM for extra control over the key life cycle.
>
>    - Enforce Transport Layer Security (TLS) 1.2 by default, and use TLS 1.3 on MySQL 8.0 and later for stronger security. Set the `require_secure_transport` parameter to control TLS enforcement. Federal Information Processing Standards (FIPS)-compliant cipher suites apply automatically.
>
>    - Trust only root certificate authorities (CAs) in client configurations to avoid connection failures. Azure manages server certificates through DigiCert Global Root G2 and Microsoft RSA Root CA 2017 root CAs. Intermediate CA rotations occur routinely without announcement.
>
> - **Harden server configuration and reduce the attack surface:** Beyond encryption controls, harden server configuration by adjusting parameter-level security settings to reduce attack surface.
>
>    Review and set security-related server parameters to strengthen your security posture. Assign custom ports during server creation only if needed. Geo-restore and geo-replica operations don't support custom port configurations.
>
> - **Protect connection credentials and application secrets:** Secure database credentials and secrets through identity-based authentication and centralized secret management to eliminate password exposure risks.
>
>    - Use identity-based authentication through Microsoft Entra ID managed identities to replace static passwords with short-lived tokens. Set connection strings by using Microsoft Entra authentication to eliminate embedded passwords.
>
>    - Store remaining MySQL native authentication passwords, including the server admin password from provisioning, in Key Vault and apply restricted access.
>
>    - Monitor Key Vault access closely when you use customer-managed key encryption because loss of access makes the server unavailable within 10 minutes. Create alerts for Key Vault access failures or key deletion events, and maintain key backups with documented recovery procedures.
>
> - **Turn on security monitoring and audit logging:** Use audit logs to track connection events, admin operations, data definition language (DDL) and data manipulation language (DML) statements, and table access. Turn on the `audit_log_enabled` server parameter and select event types based on your security requirements.
>
>    Route audit logs to Azure Monitor through diagnostic settings for Log Analytics, Azure Event Hubs, or Azure Storage destinations. Use the built-in Auditing workbook for security event visualization and create alerts for unusual connection patterns and failed authentication attempts.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create dedicated [database user accounts](/azure/mysql/flexible-server/concepts-azure-ad-authentication) for each application component. Use `GRANT` statements to assign only the privileges necessary for specific databases and tables. <br><br> Define separate accounts for read-heavy web-tier queries, write-heavy background processing, and admin tasks. Review and audit privilege assignments regularly, remove unused accounts, and limit server admin usage to break-glass procedures. | Limits the blast radius of compromised credentials to a specific database or table scope. Prevents unauthorized privilege escalation between application tiers. <br><br> Supports compliance requirements for separation of duties and least privilege access across database environments. |
| Set up [Microsoft Entra-only authentication](/azure/mysql/flexible-server/concepts-azure-ad-authentication) with a user-assigned managed identity and designated Microsoft Entra admin for user provisioning. Migrate existing connections to token-based authentication and update client libraries before you turn off MySQL authentication. | Eliminates password-based authentication vulnerabilities and credential management overhead. Centralizes identity governance through Microsoft Entra ID conditional access policies and multifactor authentication (MFA). <br><br> Enforces consistent authentication policy across both application and admin access paths. |
| Deploy servers that have [virtual network integration](/azure/mysql/flexible-server/how-to-manage-virtual-network-portal) or private endpoints for production workloads. Choose virtual network integration when the server must reside within a specific virtual network subnet. <br><br> Choose the connectivity mode before server deployment because this setting is permanent. Verify that the delegated subnet has sufficient IP address space for the server and potential replicas, and set up the `Microsoft.DBforMySQL/flexibleServers` delegation. | Eliminates public internet exposure for database traffic and keeps data within the Azure backbone and customer-controlled network boundaries. Meets private connectivity compliance requirements. |
| Set [`tls_version` to TLS 1.3](/azure/mysql/flexible-server/security-tls) on MySQL 8.0 servers and turn on `require_secure_transport` to prevent unencrypted connections. Verify that all client applications and drivers support TLS 1.3 before you enforce the change in production. | Provides the strongest encryption protocol that offers improved performance over TLS 1.2. Prevents unencrypted connections that expose data to interception and aligns with industry standards for data-in-transit protection. |
| Create a Key Vault or Managed HSM instance in the same region as the server that has soft delete and purge protection turned on. Set retention to 90 days and generate an RSA 2048, 3072, or 4096 asymmetric key. Review the [customer-managed key requirements](/azure/mysql/flexible-server/concepts-customer-managed-key) for complete prerequisites. <br><br> Assign the Key Vault Crypto Service Encryption User role to the server's user-assigned managed identity. [Set up customer-managed key encryption](/azure/mysql/flexible-server/security-how-to-data-encryption-portal#configure-customer-managed-key) through the portal, set a resource lock on the Key Vault to prevent accidental deletion, and monitor key access through audit logs. | Provides full control over the encryption key life cycle including creation, rotation, and revocation. Supports separation of duties between key management and database administration personnel. <br><br> Meets regulatory compliance requirements that mandate customer-controlled encryption keys. |
| Turn off `local_infile` to prevent client-side file loading into the database. <br><br> Review and restrict `log_bin_trust_function_creators`, `validate_password`, and other [parameters that affect security posture](/azure/mysql/flexible-server/security-tls). Document the approved parameter configuration as a security baseline. <br><br> Audit parameter changes through Azure Activity Log and use Azure Policy to enforce necessary configurations across server instances. | Reduces attack surface by turning off unnecessary features, prevents configuration drift that introduces vulnerabilities, and establishes a consistent security posture through policy-enforced configurations. |
| Store the server admin password in a [Key Vault secret](/azure/key-vault/secrets/quick-create-portal) and use Azure RBAC to restrict access to emergency scenarios only. Turn on Key Vault audit logging to track all access to stored credentials. <br><br> Transition application connections to Microsoft Entra ID token-based authentication by using managed identities. Remove hardcoded passwords from application code, configuration files, and environment variables. Audit continuous integration and continuous delivery (CI/CD) pipelines for embedded credential references. | Centralizes credential storage in a secured, audited vault instead of distributed configuration files and reduces the risk of credential exposure through source code repositories or deployment artifacts. <br><br> Creates an auditable access trail for all database credential retrievals. |
| Set `audit_log_enabled` to `ON` and select event types including `CONNECTION_V2` for authentication tracking, `DML` for data access, and `DDL` for schema changes. Route [MySqlAuditLogs](/azure/mysql/flexible-server/concepts-monitor-mysql) to Log Analytics through diagnostic settings. <br><br> Create alerts for unusual connection patterns, failed authentication attempts, and admin operations. Use the built-in Auditing workbook for security event visualization and run Kusto queries to investigate specific events. | Provides an auditable record of database access and admin operations, helps detect unauthorized access attempts, and supports compliance requirements for activity logging and monitoring. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Database for MySQL and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Develop a cost model for database workload expenses:** Azure Database for MySQL flexible server costs span multiple billing areas including compute, storage, I/O, and replication. Build a cost model that accounts for all these areas and their interdependencies.
>
>   - Compare pay-as-you-go pricing that charges hourly for compute and per gigabyte (GB) for storage against reserved instances that offer discounts for longer terms. Account for compute costs that vary significantly across tiers. Burstable is the lowest-cost option and Memory Optimized is the highest.
>
>   - Understand that storage is billed per GB per month and can only scale up after provisioning. Initial sizing decisions affect long-term costs.
>
>   - Expect HA to double compute cost because it deploys a standby replica that has dedicated storage and compute. Read replicas incur independent charges at the same rates as the primary server.
>
>   - Calculate backup storage costs. Backup storage is free up to 100% of provisioned server storage, but geo-redundant backup costs twice the locally redundant rate. Include extra feature costs that vary by tier and HA configuration.
>
> - **Monitor database cost metrics and spending trends:** Use Azure Monitor metrics to gain visibility into compute utilization, storage consumption, input/output operations per second (IOPS) usage, and backup storage, which directly correlate with service costs.
>
>    Monitor CPU percentage and memory usage to identify overprovisioned servers that consistently run below capacity. Review I/O utilization metrics to assess whether the current IOPS model aligns with actual consumption patterns. Analyze usage patterns over time to identify candidates for reserved instance purchases or tier changes.
>
>    Track storage usage trends because you can only increase storage after provisioning, not decrease it. Monitor the *Backup Storage Used* metric to understand backup costs relative to the free allowance.
>
>    Watch for storage autogrow events that increase costs as the server approaches capacity limits. Apply resource tags to servers for cost allocation across teams, projects, or environments.
>
> - **Optimize server configuration to reduce ongoing costs:** Rightsize compute tier, storage, and backup settings to match actual workload requirements and avoid overspending on unused capacity.
>
>   - Three compute tiers provide different cost-performance trade-offs. Use Burstable for intermittent workloads, General Purpose for balanced workloads, and Memory Optimized for memory-intensive workloads. Select the tier that matches your workload pattern to avoid overspending.
>
>   - Set backup retention to the minimum period that meets recovery requirements. This setting minimizes backup storage costs beyond the free allowance. Use locally redundant backup storage for workloads that don't require geo-disaster recovery. Geo-redundant storage doubles costs.
>
>   - Provision storage conservatively because storage can only scale up. Turn on autogrow as a safety net rather than overprovisioning initially.
>
>   - Compare hardware generation pricing across regions. The same tier can have different costs depending on which generations are available in each region.
>
> - **Establish spending controls for database resources:** Use Azure Policy, RBAC, and cost management to provide governance mechanisms that prevent costly database configurations and unauthorized resource changes.
>
>    Apply RBAC and use least privilege access to limit who can create, resize, or set up HA on database servers. Require approval workflows for changes that significantly affect costs like turning on HA, adding read replicas, or upgrading compute tiers.
>
>    Watch for storage autogrow events and IOPS autoscale consumption that can cause cost increases without explicit user action. Track read replica creation and configuration changes that add independent billing for compute and storage.
>
> - **Optimize database configurations for each environment:** Production and nonproduction environments have fundamentally different reliability, performance, and cost requirements that should drive database configuration choices. Set up each environment appropriately to optimize costs.
>
>   - Select the General Purpose or Memory Optimized tier for production based on workload performance requirements, and evaluate whether zone-redundant HA justifies double the compute cost. Size read replicas appropriately for their actual read workload rather than mirroring primary server specifications.
>
>   - Use the free tier for proof-of-concept and initial development. Reduce backup retention to 1-7 days and select locally redundant backup storage for nonproduction environments.
>
>   - Deploy development servers in cost-effective regions when data residency requirements permit.
>
> - **Consolidate database resources to improve cost efficiency:** Azure Database for MySQL flexible server supports hosting multiple databases on a single server instance, which distributes fixed infrastructure costs across workloads. Evaluate consolidation candidates based on compatible performance requirements, security isolation needs, and acceptable noisy-neighbor risk to improve resource utilization.
>
>    Consolidate workloads that have different peak usage patterns to maximize benefits from sharing server resources. Plan for cumulative storage growth because provisioned storage can only scale up.
>
>    Deploy read replicas for analytical and reporting workloads instead of provisioning separate servers. Size read replica compute independently from the primary server to match actual read workload requirements. The Burstable tier doesn't support read replicas, so use General Purpose or Memory Optimized tiers. Read replicas bill independently for both compute and storage.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create [budget alerts](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) at multiple thresholds in Microsoft Cost Management for database resource groups and turn on forecast alerts to detect trending overruns before they occur. <br><br> Set up anomaly detection to identify unexpected cost spikes from storage autogrow or IOPS consumption changes. | Detects cost overruns and unexpected spending patterns before they affect the budget. Provides early warning for silent cost increases from autogrow and autoscale IOPS. |
| Rightsize the [compute tier](/azure/mysql/flexible-server/concepts-compute-storage) based on actual workload utilization patterns. Evaluate CPU credit consumption on Burstable tier servers to determine whether the workload requires upgrading to the General Purpose tier. <br><br> Assess whether General Purpose servers that have consistently high memory pressure can benefit from the Memory Optimized tier. Use compute scaling to adjust vCores when workload demand changes. The scaling event requires a server restart that takes 60-120 seconds. | Eliminates overspending on compute capacity that exceeds actual workload requirements and matches compute costs to workload demand by selecting the most cost-effective tier. |
| Purchase [reserved instances](/azure/mysql/flexible-server/concept-reserved-pricing) for servers that have predictable sustained compute usage. Select 1-year or 3-year reservations. Longer commitments yield greater savings. <br><br> Use vCore size flexibility to apply reservations across different compute sizes within the same performance tier and region. Determine the right reservation size based on total vCore usage across all servers in the target region and tier. | Reduces compute costs compared to pay-as-you-go pricing for stable production workloads and provides budget predictability through fixed upfront or monthly commitment payments. |
| Apply [Azure Policy](/azure/governance/policy/overview) to restrict allowed compute tiers and SKU sizes, which prevents accidental provisioning of oversized instances. <br><br> Enforce tagging policies on database servers to make sure cost allocation tags exist for financial tracking. Limit deployment regions to prevent cross-region deployments that increase data transfer costs. | Prevents accidental provisioning of high-cost configurations through automated policy enforcement and ensures consistent cost tracking through mandatory resource tagging. |
| Select [Burstable B-series instances](/azure/mysql/flexible-server/concepts-compute-storage) for development and testing workloads that have intermittent CPU demand. The Burstable tier doesn't support HA or read replicas, which aligns with nonproduction tolerance for brief outages. <br><br> Start with the smallest Burstable SKU and scale up only if workload testing requires more resources. | Reduces nonproduction compute costs compared to the General Purpose tier. Matches compute capacity to intermittent development and testing workload patterns. |
| Use the [stop/start capability](/azure/mysql/flexible-server/how-to-stop-start-server-portal) to stop nonproduction servers outside business hours or between testing cycles. Account for the 30-day maximum stop duration because Azure automatically restarts stopped servers after that period. <br><br> Stopped servers still incur charges for provisioned storage, provisioned IOPS, and backup storage within the retention window. | Eliminates compute charges for servers that sit idle during nights, weekends, or between development sprints. Preserves the server configuration and data. |
| Consolidate multiple databases onto shared [flexible server instances](/azure/mysql/flexible-server/how-to-create-manage-databases) after you evaluate current per-database utilization to identify servers that run below capacity. <br><br> A higher-tier server that hosts multiple workloads often costs less than running separate lower-tier servers for each database. | Improves resource utilization by sharing compute and storage across multiple databases on fewer servers, which reduces total compute cost by eliminating redundant per-server overhead. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Database for MySQL.

> [!div class="checklist"]
>
> - **Define server infrastructure by using code templates:** Use infrastructure as code (IaC) through Azure Resource Manager templates (ARM templates), Bicep, Terraform, or the Azure CLI for declarative flexible server provisioning.
>
>   - Define HA mode, backup redundancy, network access mode, and MySQL version correctly in the initial deployment template. You can't change these settings after server creation unless you re-create the server.
>
>   - Parameterize the compute tier, SKU, storage size, IOPS, and backup retention for environment-specific variations.
>
>   - Deploy networking resources before server creation when you use virtual network integration, then set up diagnostic settings and RBAC as post-provisioning layers.
>
>   - Set up drift detection to identify parameters changed manually through the portal instead of through your IaC workflows.
>
> - **Automate database schema deployments in pipelines:** Integrate database schema changes into deployment pipelines by using migration scripts with validation gates and rollback capability. Use tools like Flyway, Liquibase, or custom scripts to version control schema changes and apply them through CI/CD pipeline stages.
>
>    Include premigration validation to check for GTID-incompatible operations when you target HA-enabled servers. HA-enabled servers don't support statements like `CREATE TABLE ... SELECT`. Test schema changes against nonproduction servers by using the Burstable tier to reduce costs and check compatibility.
>
> - **Set up monitoring and logging:** Set up Azure Monitor integration to collect metrics, resource logs, diagnostic settings, and workbook templates for operational visibility. Collect platform metrics for CPU, memory, storage, I/O, connections, and replication lag at one-minute frequency with 30-day retention.
>
>    Balance the detail level of logs with the performance impact because verbose logging affects server throughput. Set up the server logs feature for short-term file-based access alongside diagnostic settings for long-term analytics. Plan log routing to Event Hubs for security information and event management (SIEM) integration or Azure Storage for compliance archival.
>
> - **Define operational procedures for routine and emergency response:** Establish documented procedures that cover change management, maintenance coordination, and emergency recovery to ensure consistent operational practices across routine and critical scenarios.
>
>   - Require on-demand backups as a gate before schema migrations, parameter changes, and version upgrades to establish rollback points. Track backup consumption against the 50-backup-per-server limit and schedule cleanup of outdated backups.
>
>   - Reschedule maintenance events when they conflict with critical business periods. Only the General Purpose and Memory Optimized tiers support this capability. Set up Azure Service Health alerts to receive notifications about maintenance events and service incidents.
>
>   - Keep a runbook for recovering deleted servers within a five-day recovery window. Include escalation paths and validation steps. Document post-restore reconfiguration steps. Restored servers need HA reactivation, firewall rule updates, and parameter resets for `time_zone` and `event_scheduler`.
>
> - **Establish safe deployment practices for server updates:** Coordinate maintenance windows, patching strategies, and parameter changes to minimize disruption while maintaining security and functionality. Balance near-zero-downtime capabilities with proper testing and workload-aware scheduling.
>
>   - Set up a custom maintenance window for production workloads to control when maintenance occurs. To minimize disruption, the service manages maintenance by using near-zero-downtime patching. Use early-access maintenance policies for nonproduction servers to check patch compatibility before production rollout.
>
>   - Make sure all tables have primary keys for the near-zero-downtime patching mechanism. Set up retry logic in the data access layer to handle brief connection interruptions during maintenance, and schedule maintenance during low-workload periods.
>
>   - Distinguish dynamic parameters that apply immediately to new connections from static parameters that require server restart. Account for how static parameter changes restart both primary and standby servers when HA is turned on. Test parameter changes in nonproduction environments.
>
> - **Automate management and monitoring tasks:** Use built-in automation features and Azure Monitor integration to reduce manual operational overhead. Set up automated backups with adjustable intervals and retention periods to maintain RPOs. Review Azure Advisor recommendations periodically for optimization suggestions about performance, reliability, and cost.
>
>    Automate configuration management by using Azure CLI or REST API scripts to apply consistent server parameter configurations across server fleets. Set up automated compliance checks to compare current parameters against baseline configurations and detect drift. Schedule on-demand backups through automation before planned maintenance or change operations.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Include HA mode, backup redundancy, network access mode, MySQL version, and `lower_case_table_names` as explicit template parameters because you can't change these settings after server creation. <br><br> Parameterize the compute tier, SKU name, storage size, IOPS, and backup retention for environment-specific deployments. <br><br> Use [Bicep or ARM template modules](/azure/mysql/flexible-server/quickstart-create-bicep) from the MySQL flexible server quickstart references as starting points. Extend these templates to include diagnostic settings, firewall rules, and RBAC assignments. | Prevents costly server re-creation by capturing all immutable settings in the initial deployment template. Environment parameterization supports consistent provisioning across development, staging, and production environments. |
| Turn on the [server logs feature](/azure/mysql/flexible-server/how-to-server-logs-portal) to capture slow query logs and audit logs as downloadable files for short-term operational access. Set the `log_output` server parameter to `FILE` so that you can list and download log entries through the portal, CLI, or REST API. <br><br> Set up diagnostic settings alongside server logs to route `MySqlSlowLogs` and `MySqlAuditLogs` to a Log Analytics workspace for long-term retention and Kusto Query Language (KQL)-based analysis. | Server logs provide immediate file-based access to slow query and audit log data for rapid troubleshooting without requiring Log Analytics queries. <br><br> Combining server logs with diagnostic settings provides both short-term operational access and long-term analytics retention. |
| Turn on `audit_log_enabled` in server parameters and select event types like `CONNECTION_V2`, `DML_NONSELECT`, `DDL`, and `DCL` that match compliance requirements. <br><br> Use `audit_log_include_users` and `audit_log_exclude_users` to scope logging to application and admin accounts and exclude service accounts. <br><br> Create a diagnostic setting to send `MySqlAuditLogs` to a Log Analytics workspace. Use the [Auditing workbook](/azure/mysql/flexible-server/tutorial-configure-audit) to visualize connection events, admin actions, and table access summaries. | Provides database-level activity tracking for compliance audits that logs connection attempts, schema changes, and privilege modifications. The Auditing workbook delivers ready-made visualizations to audit event analysis without requiring custom KQL queries. |
| Require an [on-demand backup](/azure/mysql/flexible-server/concepts-backup-restore) before schema migrations, parameter changes, or version upgrades to create a known-good rollback point. <br><br> Track backup usage against the 50-backup-per-server limit and incorporate cleanup of outdated backups into operational routines. <br><br> Include restore validation in the deployment checklist so teams can confirm that the backup actually works before they proceed with the change. | Provides an explicit recovery point for each change, which reduces potential data loss if you require rollback. This approach embeds backup discipline into the change management workflow rather than relying on automated daily snapshots alone. |
| Add a `CanNotDelete` management lock to production MySQL flexible server resources to prevent accidental deletion through the portal, the CLI, or automation. Include lock resources in IaC templates to apply locks automatically during provisioning. <br><br> Document the [deleted server recovery procedure](/azure/mysql/flexible-server/concepts-backup-restore) as a fallback for situations where teams didn't apply management locks. You can recover deleted servers within five days. | Prevents irreversible data loss from accidental deletion of production database servers and their associated data. <br><br> Management locks provide a safety net without affecting normal operational tasks like configuration changes or restarts. |
| Select the [Custom Managed Maintenance Window policy](/azure/mysql/flexible-server/how-to-maintenance-portal) and specify the preferred day and 60-minute UTC start time that aligns with lowest workload periods. Assign servers to maintenance Batch 1 or Batch 2 to stagger updates between test and production environments. <br><br> Monitor upcoming maintenance through the portal maintenance status section and reschedule when conflicts arise with planned business events. | Aligns planned maintenance with lowest traffic periods to minimize workload disruption during patching. Staggering batches between environments provides early warning of problems before production receives updates. |
| Automate [server parameter configuration](/azure/mysql/flexible-server/how-to-configure-server-parameters-cli) across server fleets to apply consistent baselines for slow query logging, audit logging, and connection limits. <br><br> Script periodic parameter exports and compare them against a stored baseline configuration to detect drift from the portal or unplanned changes. <br><br> Integrate parameter validation into CI/CD pipelines so infrastructure deployments verify that critical server parameters match the expected configuration before completing. | Consistent parameter baselines across environments prevent configuration-related incidents caused by unplanned portal changes. <br><br> Automated drift detection identifies unauthorized or accidental parameter modifications before they affect workload behavior. |
| Turn on the [storage autogrow](/azure/mysql/flexible-server/concepts-service-tiers-storage#storage-autogrow) setting to automatically increase storage capacity when utilization approaches the provisioned limit. Monitor storage percentage metrics alongside autogrow events to forecast storage growth trends and plan capacity adjustments. <br><br> Keep in mind that storage can only scale up and never decrease when you plan initial storage allocation and autogrow thresholds. | Prevents the server from entering read-only mode when storage capacity runs out, which maintains write availability for database operations. Automatic scaling eliminates the need for manual intervention during storage growth events. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Database for MySQL.

> [!div class="checklist"]
>
> - **Run capacity planning:** Your compute, storage, and IOPS decisions determine your maximum performance. Run capacity planning to evaluate vCore and memory needs based on query complexity, concurrent connections, and workload type. Account for InnoDB buffer pool memory allocation, which varies by compute size and determines cache efficiency. Also consider `max_connections` limits, which scale with available memory.
>
>    Estimate storage growth. You can only scale up allocation, not reduce it. Calculate IOPS requirements based on read/write ratios, transaction volume, and peak demands. Use *Storage IO Percent* and *Storage IO Count* metrics to define baseline usage patterns.
>
>    Plan for connection pooling when concurrent application connections approach `max_connections` limits. Identify read versus write workload ratios to determine whether read replicas provide the horizontal read scale that your workload requires. Include failover capacity in your planning.
>
> - **Choose compute tiers and SKUs based on performance requirements:** Azure Database for MySQL offers three compute tiers that have distinct performance characteristics and feature availability. Match tier selection to workload performance needs, scalability requirements, and feature dependencies.
>
>   - Select the General Purpose tier for production workloads that require sustained compute capacity without throttling. This tier provides balanced compute and memory with support for HA, read replicas, and accelerated logs. Use this tier for most production scenarios that require predictable performance and enterprise features.
>
>   - Choose the Memory Optimized tier when workloads require higher memory-to-vCore ratios for cache-dependent and high-concurrency scenarios. This tier delivers the lowest latency and highest queries per second (QPS). Use this tier for memory-intensive workloads that have large working sets or high-concurrency requirements.
>
>   - Limit the Burstable tier to development and testing environments because the CPU credit model throttles under sustained load. This tier doesn't support HA, read replicas, or accelerated logs, which aligns with nonproduction requirements.
>
>   - Account for IOPS limits that scale with compute size. Make sure InnoDB buffer pool allocation matches working set requirements because it varies by SKU. Select General Purpose or Memory Optimized tiers when you need HA, read replicas, or accelerated logs support.
>
> - **Define a scaling strategy:** Plan compute tier changes for low-traffic periods. These changes require server restarts that take 60-120 seconds, which causes brief workload disruption. Apply storage increases online, but remember that you can't reduce allocation. Use autoscale IOPS to provide automatic I/O scaling based on demand without manual intervention.
>
>    Account for read replicas by using asynchronous binary log replication, which introduces measurable lag between source and replica data. Select General Purpose or Memory Optimized tiers for replica support, and set up application-level routing because each replica has its own connection endpoint. Create replicas with specifications equal to or more than the source to prevent lag accumulation.
>
> - **Monitor database performance metrics:** Monitor *CPU Percent*, *Memory Percent*, and *Storage IO Percent* to identify resource saturation before it affects query performance. Monitor InnoDB buffer pool metrics like `pages_data`, `pages_free`, and read request ratios to measure cache efficiency and memory pressure.
>
>    The `Slow_queries` count identifies queries that exceed the `long_query_time` threshold for optimization. InnoDB *Row Lock Time* and *Row Lock Waits* identify lock contention that affects transaction throughput, and `lock_deadlocks` and `lock_timeouts` indicate concurrency conflicts that require query or schema changes.
>
>    Use Azure Monitor metrics, slow query logs, and the Query Performance Insight workbook for query-level visibility. Use Log Analytics for historical trend analysis through Kusto queries.
>
> - **Run performance testing:** Create load test workloads that reflect production query patterns, including read/write ratios, transaction complexity, and concurrency levels. Include peak period simulations to validate autoscale IOPS behavior and compute headroom. Use production-representative data volumes in tests because InnoDB buffer pool efficiency and query plans depend on data size.
>
>    Test compute tier scaling to measure restart duration and application reconnection behavior. Simulate read replica lag under load and verify storage autogrow activation. Capture baseline metrics for query latency, throughput, IOPS consumption, and connection count to compare across IOPS settings and detect cache sizing problems during growth.
>
> - **Optimize database performance through configuration and features:** Tune MySQL engine parameters and Azure-specific features based on workload characteristics to maximize query performance, reduce resource contention, and improve overall database efficiency.
>
>   - Set the InnoDB buffer pool size, which determines how much working data is cached in memory and directly affects the cache hit ratio. Set transaction log sizing to balance write throughput against crash recovery time. Select appropriate table storage strategy to affect performance and manageability for large databases.
>
>   - Set up connection pooling at the proxy or application layer to reduce connection creation and teardown overhead under high concurrency. To reduce capacity for active queries, monitor and close idle connections that consume server thread resources.
>
>   - Use accelerated logs to speed up transaction log operations by separating log I/O to high-performance storage. Check feature availability by compute tier and region, and benchmark performance gains to measure improvement for specific workloads.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Turn on [autoscale IOPS](/azure/mysql/flexible-server/concepts-storage-iops) for variable or unpredictable I/O patterns to scale dynamically with demand. Use pre-provisioned IOPS for workloads that have consistent, predictable throughput. <br><br> Switch between IOPS models at any time through the **Compute + storage** settings without data loss or service disruption. <br><br> Track *Storage IO Percent* and *Storage IO Count* to confirm that the model meets workload needs. Sustained values near 100% indicate throttling that requires a model change or tier upgrade. | Autoscale IOPS prevents storage throttling during workload spikes by dynamically adjusting to demand. Pre-provisioned IOPS delivers predictable performance for steady-state workloads that have fixed throughput requirements. |
| Offload read queries to [read replicas](/azure/mysql/flexible-server/concepts-read-replicas) to free capacity on the primary server for write operations. [Create replicas](/azure/mysql/flexible-server/how-to-read-replicas-portal) in the same region or across regions based on latency and recovery needs. <br><br> Use a connection proxy like ProxySQL to distribute read traffic automatically. Provision replicas with equal or greater compute and storage than the source server. <br><br> Track *Replication Lag* to detect delays and set alerts on lag that exceeds acceptable thresholds. Make sure all source tables have primary keys because missing keys increase replication latency. | Offloading read queries from the primary server frees compute and I/O capacity for write operations. Cross-region replicas bring data closer to users, which reduces read latency in distributed applications. |
| Plan [compute tier scaling](/azure/mysql/flexible-server/concepts-service-tiers-storage#scale-resources) during low-traffic periods to minimize the impact of the necessary restart. Turn on storage autogrow to prevent the server from entering read-only mode when capacity runs out. <br><br> Update replica compute to equal or exceed the source configuration before you scale the source server. Test that applications automatically reconnect and resume operations during the 60-to-120-second restart window. | Scheduled scaling reduces the impact of necessary server restarts on active workloads. Storage autogrow prevents the server from entering read-only mode, which stops write operations. |
| Set `slow_query_log` to `ON`, adjust `long_query_time` from the default 10 seconds to match your baseline, and turn on `log_queries_not_using_indexes` to capture full table scans. Send [MySqlSlowLogs](/azure/mysql/flexible-server/concepts-monitor-mysql) to a Log Analytics workspace for [Kusto query analysis](/azure/mysql/flexible-server/tutorial-query-performance-insights). | Pinpoints specific queries that cause performance degradation through execution time and scan-pattern analysis. Historical trend data detects gradual regressions before they affect users. |
| Create [alerts](/azure/mysql/flexible-server/concepts-monitor-mysql-reference#metrics) on *CPU Percent*, *Memory Percent*, and *Storage IO Percent* when they approach saturation. Alert on *Storage Percent* to detect capacity limits before autogrow triggers, and monitor *Replication Lag* on read replicas. <br><br> Alert on increasing InnoDB *Row Lock Waits* and `lock_deadlocks` to detect concurrency bottlenecks. Monitor `Slow_queries` trends and track *Aborted Connections* to identify connection problems that affect performance. | Detects resource saturation and engine contention before they cause query timeouts or application failures. Supports proactive response to performance degradation through automated notification of threshold breaches. |
| Turn on [accelerated logs](/azure/mysql/flexible-server/concepts-accelerated-logs) through the **Compute + storage** settings for Memory Optimized or General Purpose tiers. Plan for a server restart during activation and verify that the server has an allocated availability zone. <br><br> When accelerated logs are active, you can't turn off storage autogrow and the `binlog_expire_logs_seconds` parameter is ignored. Test workload performance before and after activation to quantify throughput and latency gains. | Reduces transaction commit latency by placing transaction logs on high-performance storage. Increases query throughput in high-concurrency scenarios at no extra cost on the Memory Optimized tier. |
| Set `innodb_buffer_pool_size` to the maximum value that the compute SKU supports for the best cache hit ratio. Choose [`innodb_log_file_size`](/azure/mysql/flexible-server/concepts-server-parameters) based on write volume. Larger sizes increase crash recovery time. <br><br> Keep `innodb_tmpdir` at the default `/mnt/temp` for SSD-speed temporary sort operations during online `ALTER TABLE`. <br><br> Turn on `innodb_file_per_table` for databases that have tables near the 8-terabyte (TB) system tablespace limit. Adjust `sort_buffer_size` if `Sort_merge_passes` indicates excessive merge passes during sorting. | Dedicating available memory to InnoDB buffer pool caching maximizes page cache efficiency. Appropriate transaction log sizing balances write throughput against crash recovery time. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Database for MySQL and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Microsoft Entra-only authentication is enabled on flexible servers.
- Microsoft Entra authentication is configured for centralized identity management.
- Customer-managed keys are used for encryption at rest.

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Database for MySQL](/azure/governance/policy/samples/built-in-policies#azure-database-for-mysql) and other policies that might affect the security of the database.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [WordPress on Azure App Service](/azure/architecture/example-scenario/infrastructure/wordpress-app-service).

## Related content

- [Azure Database for MySQL flexible server](/azure/mysql/flexible-server/overview)
- [Training: Azure Database for MySQL flexible server](/training/modules/implement-azure-database-for-mysql-features-that-support-mission-critical-workloads/)
