---
title: Architecture Best Practices for Azure SQL Managed Instance
description: Learn how to build resilient, secure, and efficient database workloads on Azure SQL Managed Instance by following Well-Architected Framework best practices.
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: concept-article
ms.date: 04/01/2026
ms.subservice: waf-service-guide
products:
  - azure-sql-managed-instance
azure.category:
  - databases
---

<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD025 -->

# Architecture best practices for Azure SQL Managed Instance

Azure SQL Managed Instance is a fully managed platform as a service (PaaS) database engine that provides near-complete compatibility with the latest SQL Server Enterprise Edition. It combines an instance-scoped deployment model with virtual network-native networking, delivering broad SQL Server feature support alongside the operational advantages of a managed platform. SQL Managed Instance targets SQL Server workloads that rely on instance-scoped features like cross-database queries, SQL Server Agent, Service Broker, and common language runtime (CLR) integration.

This article assumes that as an architect, you've reviewed the [data store decision tree](/azure/architecture/guide/technology-choices/data-store-decision-tree) and chose Azure SQL Managed Instance as the database engine for your workload.

The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- markdownlint-disable-next-line MD036 -->
**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure SQL Managed Instance

> [!NOTE]
> This service guide builds on guidance in the [Azure SQL Database service guide](./azure-sql-database.md). SQL Managed Instance shares the SQL Server database engine with SQL Database but uses an instance-scoped deployment model with distinct architecture, networking, and feature capabilities. Review the SQL Database guide for shared platform guidance. This guide focuses on SQL Managed Instance-specific capabilities and architectural considerations.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review SQL Managed Instance quotas, limits, and known issues:** SQL Managed Instance enforces regional deployment [limits](/azure/azure-sql/managed-instance/resource-limits), database counts, and storage ceilings that vary by subscription type and tier. Higher-tier deployments consume more vCore quota, which constrains regional capacity planning.
>
>   - Factor in [known problems](/azure/azure-sql/managed-instance/doc-changes-updates-known-issues) that affect failover groups, backup behavior during replication link usage, and migration constraints from older SQL Server versions. Review these limitations during initial design to avoid constraints that you can't easily fix later.
>
> - **Anticipate potential failures:** Use failure mode analysis to anticipate failures and plan mitigations for SQL Managed Instance.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Management operations take hours and block other operations on the instance or in the subnet | Plan scaling operations during maintenance windows. Avoid concurrent management operations and account for multihour durations in recovery plans. |
>    | Initial seeding of a failover group can time out because of slow network links or large database sizes | Use global virtual network peering for highest bandwidth. Monitor seeding progress and plan for duration based on database size and link speed. |
>    | Management operations on one instance in a subnet can block or delay operations on other instances that share the same virtual cluster | Isolate production instances in dedicated subnets to prevent cross-impact from development or test instance operations. |
>
> - **Build redundancy through zone distribution and appropriate tier selection:** Use zone redundancy and appropriate tier selection to eliminate single points of failure within a region.
>
>   - Turn on zone-level redundancy to distribute replicas across availability zones. Architecture and eligibility differ by tier and deployment configuration.
>
>   - Assess redundancy effects on performance and dependent services, including cross-zone latency on online transaction processing (OLTP) workloads and alignment of backup storage with zone-redundant deployments.
>
> - **Plan scaling around management operation lead times:** Management operations take hours rather than minutes, which requires a different approach to scaling strategy and capacity planning.
>
>   - Anticipate extended timelines for first-in-subnet creation, which takes significantly longer because of virtual cluster provisioning. Concurrent operations queue sequentially within a subnet.
>
>   - Size subnets with future scaling headroom. Undersized subnets constrain growth, and mixed hardware generations create separate virtual clusters that consume extra IP addresses.
>
>   - Preprovision standby instances when you need strict scaling response times. Isolate production workloads in dedicated subnets to prevent cross-instance operation blocking.
>
> - **Configure monitoring and alerting for instance health and replication status:** Prioritize alerts on the metrics that signal reliability risks early. Track instance availability through Resource Health events, monitor management operation status transitions through an activity log, and verify automated backup completion regularly because backup failures can surface silently when storage fills or Domain Name System (DNS) problems arise. For instances configured with failover groups, alert on replication lag against recovery point objective (RPO) thresholds to detect geo-replication drift before it causes unacceptable data-loss exposure.
>
>    Failover group status transitions reveal disruptions before they escalate to recovery gaps.
>
> - **Implement connection resiliency and self-preservation techniques:** Build application-level resilience to absorb transient faults from planned maintenance, failovers, and DNS role-switching events.
>
>   - Add retry logic with exponential backoff that accounts for real failover durations and extended DNS update windows during geo-failover.
>
>   - Prepare for cold-cache performance degradation after General Purpose tier failovers, where the instance restarts on a new node without warm replicas.
>
>   - Schedule maintenance windows during low-traffic periods. Stagger windows across failover group pairs to avoid simultaneous maintenance on both members.
>
> - **Design disaster recovery strategy by using failover groups:** Failover groups replicate all user databases as a unit to a geo-secondary instance in another region, but system databases aren't included. You must synchronize sign-ins, credentials, SQL Agent jobs, and instance-level settings independently to prevent authentication failures and operational gaps after failover.
>
>    Use failover group listener endpoints in connection strings to avoid changes during failover. Synchronize retention policies between members because configuration changes don't replicate. For hybrid environments, the Managed Instance link offers an alternative replication path.
>

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Turn on [zone redundancy](/azure/azure-sql/managed-instance/instance-zone-redundancy-configure) for production SQL Managed Instance workloads that require datacenter failure protection. You can configure zone redundancy during instance creation or convert existing instances. Verify tier eligibility before you proceed. | Achieves higher service-level agreement (SLA) guarantees than locally redundant deployments and survives datacenter-level outages without application connection changes.<br><br>Eliminates the availability zone as a single point of failure, which means planned maintenance in one zone doesn't force a workload-visible failover. |
| Size the delegated subnet to accommodate current instance requirements plus future scaling headroom. Use the [subnet sizing calculator](/azure/azure-sql/managed-instance/vnet-subnet-determine-size) to determine the minimum IP address space for anticipated instance counts across hardware and tier configurations.<br><br>Add buffer capacity beyond minimum requirements because subnet resizing requires complex migration operations. Account for virtual cluster boundaries, where different hardware generations and tier combinations consume extra subnet IP addresses. | Adequate subnet sizing prevents scaling bottlenecks that require subnet migration to resolve. Overprovisioned IP address space costs little and provides flexibility for future instance growth and tier changes. |
| Set up [Azure Monitor](/azure/azure-sql/managed-instance/monitoring-sql-managed-instance-azure-monitor) alerts for critical reliability metrics like instance availability, replication lag, and backup health. Create alert rules that trigger before replication lag breaches RPO thresholds. Track storage consumption to prevent backup failures. | Proactive alerting lets you respond rapidly to replication drift and instance health problems before they escalate to data loss or extended outages.<br><br>Early detection of backup failures prevents gaps in recovery point coverage that could go unnoticed until you need a restore. |
| Add retry logic with [exponential backoff](/azure/azure-sql/database/develop-overview#resiliency) that accounts for SQL Managed Instance [reconnection behaviors](/azure/azure-sql/database/troubleshoot-common-connectivity-issues) during failover events. Extend retry timeouts for failover group listener scenarios to cover the DNS role-switching window. | Retry logic absorbs transient connectivity disruptions from maintenance and failover events, which prevents application errors from reaching users. Applications recover automatically from brief outages without manual intervention. |
| Set up [maintenance windows](/azure/azure-sql/managed-instance/maintenance-window) to shift planned failover events away from the default schedule. Select windows that align with your lowest workload activity.<br><br>For failover group pairs, assign different maintenance window schedules to primary and secondary instances to prevent simultaneous maintenance. | Maintenance windows shift planned failover events to predictable, low-traffic periods, which reduces the effect of transient connection interruptions on users.<br><br>Staggered schedules across failover group instances prevent simultaneous maintenance on both members. |
| Set up instance-scoped [failover groups](/azure/azure-sql/managed-instance/failover-group-configure-sql-mi) with a customer-managed failover policy for cross-region disaster recovery.<br><br>Create a geo-secondary instance in a paired region that has a matching configuration and the same DNS zone. Establish virtual network connectivity between subnets by using global virtual network peering for lowest-latency replication. | Failover groups provide automated geo-replication of all user databases and redirect connections through DNS-based endpoints. This approach supports cross-region recovery without application connection string changes.<br><br>Customer-managed failover policy keeps you in control of recovery decisions and timing. |
| Set up [geo-redundant backup storage](/azure/azure-sql/managed-instance/automated-backups-overview#backup-storage-redundancy) to maintain backup copies in a paired region. Use geo-restore as a fallback recovery option. [Set up long-term retention](/azure/azure-sql/managed-instance/automated-backups-change-settings#configure-backup-storage-redundancy) that uses geo-redundant storage (GRS) for compliance scenarios that require extended availability. | Geo-redundant backup storage lets you recover to any Azure region during a regional disaster, even without failover groups. This approach provides an independent recovery path that doesn't depend on real-time replication health. |
| Account for instance provisioning time in [point-in-time restore (PITR)](/azure/azure-sql/managed-instance/point-in-time-restore) recovery time objective (RTO) calculations because PITR restores to a new instance rather than an existing one.<br><br>Run test restores to measure end-to-end recovery duration, including instance provisioning. Test both first-in-subnet and existing-subnet scenarios to understand the variance in restore times. | Accurate RTO calculations that account for instance provisioning time prevent you from underestimating recovery duration during actual incidents. Validated restore times give you confidence that PITR meets your workload recovery requirements. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of SQL Managed Instance.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Establish a security baseline:** Start with the [Azure security baseline for Azure SQL](/security/benchmark/azure/baselines/azure-sql-security-baseline). Focus on the controls that the instance-scoped model most affects, including custom server-level roles for administrative segmentation, virtual network subnet delegation for network isolation, instance-level Transparent Data Encryption (TDE) key management, and operator auditing for Microsoft Support access.
>
>    SQL Managed Instance shares the Azure SQL security foundation but introduces instance-specific controls, such as Common Language Runtime (CLR) assemblies, linked servers, and SQL Agent, that expand the attack surface beyond what SQL Database exposes. Prioritize these controls in your baseline review.
>
> - **Apply segmentation strategies by using identity-based and network-level controls:** SQL Managed Instance supports identity-based segmentation through server-level and database-level roles that enforce separation of duties. Custom server-level roles provide detailed administrative segmentation not available in SQL Database. Separating control plane access from data plane access keeps instance management distinct from data operations.
>
>    Network-level segmentation relies on deploying instances into dedicated delegated subnets where network security groups (NSGs) control traffic flows. Avoid reusing route tables and NSGs across subnets that participate in virtual network peering with SQL Managed Instance subnets.
>
>    Instance pools share underlying VM resources, which affects tenant isolation guarantees when you consolidate instances. Evaluate whether workloads that have different security requirements need separate instances.
>
> - **Integrate with Microsoft Entra ID for identity and access management:** SQL Managed Instance supports Microsoft Entra authentication through multiple methods. This support centralizes identity management and supports conditional access policies. SQL Managed Instance also supports Windows authentication through Kerberos for Microsoft Entra principals, a capability not available in SQL Database. You can lift-and-shift legacy applications without code changes.
>
>    Plan the identity architecture by cataloging administrative, operator, and workload identities. Assign database roles by using least-privilege principles. Use user-assigned managed identities as the instance identity for Azure service integration.
>
> - **Implement network isolation by using Azure Virtual Network:** SQL Managed Instance deploys into a dedicated virtual network subnet, which provides network-level isolation by default and shapes connectivity and security decisions.
>
>   - Keep the public endpoint deactivated because it creates another attack surface, and failover groups and Managed Instance link only support virtual network-local connectivity.
>
>   - Delegate the managed instance subnet exclusively to the managed instance type and apply service-aided NSG rules for traffic control.
>
>   - Assign unique route tables and NSGs to each SQL Managed Instance subnet for virtual network peering. Reusing shared tables causes connectivity failures.
>
> - **Configure data encryption for at-rest and in-transit protection:** SQL Managed Instance provides multiple encryption layers that cover data at rest, in transit, and at the application level. Each layer requires distinct configuration decisions.
>
>    | Layer | Scope | Key consideration |
>    |---|---|---|
>    | TDE | Instance-level, all databases inherit the same key | Rotation affects every database simultaneously, and you must retain previous key versions for backup restoration. |
>    | Transport encryption (TLS) | Connections in transit | Enforce current Transport Layer Security (TLS) standards and strict connection modes to prevent protocol downgrade attacks. |
    | Always Encrypted | Column-level, keys stay outside the database engine | Protects data from privileged users. Secure enclaves support server-side operations on encrypted data. |
>
> - **Harden instance configurations to reduce attack surface:** SQL Managed Instance exposes capabilities beyond SQL Database that expand the attack surface. These capabilities require targeted hardening decisions alongside governance controls.
>
>   - The update policy controls security patch cadence. You face a trade-off between immediate application and Managed Instance link compatibility.
>
>   - SQL Agent, linked servers, CLR assemblies, and xp_cmdshell each require risk assessment. Deactivate unused capabilities and enforce current TLS standards.
>
>   - Use Azure role-based access control (Azure RBAC) to restrict management operations, apply resource locks, and enforce security configurations through Azure Policy.
>
> - **Protect credentials and encryption keys by using managed identities and Azure Key Vault:** Managed identities eliminate stored credentials for application-to-instance connectivity and cross-service integrations. User-assigned managed identities remove connection string secrets and simplify rotation management.
>
>    Key Vault provides unified life cycle management for TDE protectors and Always Encrypted column master keys. Key access monitoring detects unusual usage patterns that might indicate compromise.
>
>    SQL Managed Instance uses credential objects to write audit logs to blob storage. Renew these credentials before they expire to maintain audit continuity. Plan credential handoff procedures for identities that use SQL authentication during migration.
>
> - **Configure security monitoring and audit logging:** SQL Managed Instance auditing supports Azure Blob Storage, Azure Event Hubs, and Azure Monitor logs as audit targets. Operator auditing provides visibility into Microsoft Support operations on the instance. Audit log integrity requires tamper-evidence controls for compliance.
>
>    Log Analytics integration supports advanced query and correlation of security events, while Event Hubs supports external security information and event management (SIEM) integration. Microsoft Defender for SQL adds threat protection, including alerts for injection, brute-force, and unusual access patterns.
>
>    Track authentication events, query execution, and privilege changes through audit specifications to detect escalation attempts and unauthorized access.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create [custom server-level roles](/azure/azure-sql/database/security-server-roles) that scope permissions to specific administrative functions rather than granting broad privileges. Avoid assigning built-in db_owner or server admin accounts to application workloads. | Reduces the risk of privilege escalation by limiting each administrative role to the minimum permissions that it needs. Custom server-level roles let you enforce granular separation of duties that built-in roles don't provide. |
| Turn on [Microsoft Entra-only authentication](/azure/azure-sql/database/authentication-azure-ad-only-authentication) and turn off SQL-based authentication for the managed instance. Before you switch, migrate SQL Agent jobs, linked servers, and auditing credentials that depend on SQL sign-ins to Microsoft Entra principals. | Eliminates credential-based attack vectors by removing SQL authentication and enforcing modern identity protocols that support multifactor authentication. |
| Configure a [user-assigned managed identity](/azure/azure-sql/database/authentication-azure-ad-user-assigned-managed-identity) as the instance identity for Azure service integration, including TDE that uses customer-managed keys, auditing to storage, and cross-service authentication. | Removes credential management overhead for Azure service integration and reduces the risk of exposed secrets in configuration. |
| Keep the [public endpoint](/azure/azure-sql/managed-instance/public-endpoint-configure) turned off for production instances. If you require public access, restrict it to specific IP address ranges by using NSG rules. Use virtual network-local connectivity for standard access and [private endpoints](/azure/azure-sql/managed-instance/private-endpoint-overview) for cross-virtual network scenarios. | Shrinks the network attack surface to authorized virtual network paths, which simplifies compliance audits and reduces the blast radius of compromised credentials. |
| Configure NSG rules on the [SQL Managed Instance subnet](/azure/azure-sql/managed-instance/connectivity-architecture-overview) to allow only required inbound traffic from authorized sources. Apply a deny-by-default approach while preserving service-aided mandatory rules for instance operations. | Prevents lateral movement from compromised resources in adjacent subnets, which limits blast radius during a network-level breach. |
| Set up [TDE by using customer-managed keys](/azure/azure-sql/database/transparent-data-encryption-byok-overview) in a key vault that has soft-delete and purge protection turned on. Use a user-assigned managed identity for key access. Turn on [automated rotation](/azure/azure-sql/managed-instance/scripts/transparent-data-encryption-byok-powershell) and dedicate a key vault to SQL Managed Instance resources. | Maintains full control over the encryption key life cycle, including rotation and revocation, and meets organizational key management compliance requirements. |
| Use [Microsoft Defender for SQL](/azure/azure-sql/database/azure-defender-for-sql) to activate [vulnerability assessment](/azure/azure-sql/database/sql-vulnerability-assessment) and advanced threat protection. Set up alerts for detection events and route them to security operations through Microsoft Defender for Cloud. | Detects security misconfigurations and active threats through automated scanning and behavioral analysis that integrates into your security operations workflows. |
| Turn off [SQL Agent](/azure/azure-sql/managed-instance/job-automation-managed-instance) if the workload doesn't use it. When active, avoid assigning jobs to highly privileged accounts and configure proxy accounts for steps that require specific credential access. | Prevents privilege escalation through SQL Agent by restricting job ownership and credential access, which minimizes a SQL Managed Instance-specific attack surface. |
| Store [TDE protector keys](/azure/azure-sql/database/transparent-data-encryption-byok-overview) and Always Encrypted column master keys in a key vault that has soft-delete and purge protection. Turn on Key Vault audit logging and use a dedicated vault for SQL Managed Instance to avoid throttling. | Centralizes encryption key management with a full audit trail and protects against accidental key loss that could prevent database access. |
| Turn on [server auditing](/azure/azure-sql/managed-instance/auditing-configure) and set up event groups to capture sign-in activity and completed queries. Configure dual destinations. Use blob storage for long-term retention and Azure Monitor logs for real-time analysis.<br><br>Create a separate server audit that has operator auditing turned on to track Microsoft Support operations. Turn on immutable storage on audit blob containers to prevent record tampering. | Maintains a full audit trail of database activity for compliance requirements, incident investigation, and anomaly detection. |
| Configure [diagnostic settings](/azure/azure-sql/managed-instance/auditing-configure) to send security audit events to a Log Analytics workspace. Use Kusto Query Language (KQL) queries for anomaly detection and set up alerts on suspicious activity patterns like repeated failed sign-ins. | Lets you analyze security events in real time and correlate across workloads by using centralized log aggregation and automated threat detection. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to SQL Managed Instance and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Create and maintain a cost model that accounts for the provisioned compute billing model:** SQL Managed Instance uses an always-provisioned compute model where vCores bill continuously regardless of utilization. Your cost model should account for the primary cost drivers, which include service tier selection, vCore count, hardware generation, and storage allocation based on the maximum configured size.
>
>    Instance-scoped billing means all databases share compute costs, which requires clear mapping for chargeback and showback. Include licensing and commitment levers like Azure Hybrid Benefit, reserved capacity, and license-free standby replicas. Each lever reduces cost through a different mechanism.
>
>    Regional vCore quota constraints can force multisubscription architectures, which adds complexity to cost tracking and governance.
>
> - **Monitor and analyze costs to identify optimization opportunities:** In the always-provisioned model, idle compute still incurs charges. Use Microsoft Cost Management to track compute, storage, backup, and licensing costs per instance. Compare actual storage consumption against the reserved maximum to identify billing waste.
>
>    Monitor resource utilization to identify vCore overprovisioning and validate whether the current service tier remains justified. Create budget, forecast, and anomaly alerts to detect unexpected cost increases from scaling or configuration changes.
>
> - **Optimize compute and licensing configurations to reduce provisioned resource costs:** Compute costs accrue continuously in the provisioned-only model, so rightsize vCore count and optimize licensing from the start. Analyze utilization before you select configuration. Use the General Purpose tier when workloads tolerate higher storage latency. The tier difference is the single largest cost lever.
>
>    Apply Azure Hybrid Benefit for existing SQL Server licenses, commit to reserved capacity for predictable workloads, and designate failover group secondaries as license-free standby replicas when they serve disaster recovery only.
>
> - **Optimize environment costs across production and nonproduction instances:** Identical production and nonproduction configurations waste budget in an always-provisioned model. Differentiate environment cost profiles by applying SQL Managed Instance-specific savings mechanisms to each tier.
>
>   - Optimize nonproduction environments by using stop/start on General Purpose instances during predictable idle periods to eliminate compute and licensing charges.
>
>   - Differentiate environment configurations by deploying nonproduction instances that use reduced vCore counts and dev-test subscription pricing.
>
>   - Optimize disaster recovery environments by evaluating geo-restore as a lower-cost alternative to failover groups for noncritical workloads.
>
> - **Consolidate small workloads by using instance pools to improve resource efficiency:** Instance pools consolidate multiple small instances on shared compute, which reduces per-instance overhead. Pools support smaller vCore configurations exclusive to pooled deployments and are restricted to the General Purpose tier on supported hardware generations.
>
>    Pool compute is charged at the pool level regardless of how many instances run inside it, so unused vCores still incur costs. Licensing and reserved capacity decisions apply uniformly across all instances in the pool.
>
>    Pooled instances share local disk and network resources, which can create noisy-neighbor effects. Each instance receives dedicated vCores and memory, but workloads that require stronger isolation should deploy as standalone instances.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [Compare actual storage consumption](/azure/azure-sql/managed-instance/monitoring-sql-managed-instance-azure-monitor) against the maximum configured size because SQL Managed Instance bills for reserved storage regardless of utilization. Reduce the reserved maximum on instances where allocation significantly exceeds consumption. | Identifies instances where reserved storage exceeds actual usage, so you can rightsize and reduce the per-GB billing waste in the SQL Managed Instance reserved storage model. |
| Activate [Azure Hybrid Benefit](/azure/azure-sql/azure-hybrid-benefit) on SQL Managed Instance to apply existing SQL Server licenses that have Software Assurance. Enterprise Edition licenses offer the most favorable conversion ratios, especially on the General Purpose tier.<br><br>When you stop a General Purpose instance, deactivate Azure Hybrid Benefit first and reallocate the licenses to other Azure SQL resources. Reactivate the benefit after you restart the instance. | Reduces SQL Server licensing costs by using existing on-premises licenses for cloud deployments. Enterprise Edition customers gain the largest savings on the General Purpose tier, given the favorable vCore conversion ratios. |
| Purchase [reserved capacity](/azure/azure-sql/database/reservations-discount-overview) commitments for SQL Managed Instance deployments that have predictable, long-running workloads. Apply reservations to instance pools to combine shared compute savings with commitment-based discounts.<br><br>For workloads that have predictable schedules, run multiple smaller instances across different time windows under the same vCore reservation. You can't carry forward unused reserved hours, so maximize utilization through scheduling. | Commitment-based pricing reduces costs compared to pay-as-you-go rates for stable workload configurations.<br><br>Reserved capacity paired with instance pools provides the most cost-effective deployment model for multiple small instances. |
| Designate the secondary instance in a failover group as a [license-free standby replica](/azure/azure-sql/managed-instance/failover-group-standby-replica-how-to-configure) when you use it exclusively for disaster recovery. The standby supports failovers, backups, maintenance, and disaster recovery drills but not production connections. | Eliminates SQL Server licensing costs on the disaster recovery secondary but maintains the same RPO and RTO as a readable replica.<br><br>Frees Azure Hybrid Benefit licenses for allocation to other Azure SQL resources, which increases the value of existing license investments. |
| Stop General Purpose instances during predictable idle periods to eliminate vCore and licensing charges. Create [stop/start schedules](/azure/azure-sql/managed-instance/instance-stop-start-how-to) that define pairs and set a minimum interval between successive actions.<br><br>Account for startup latency by triggering start operations ahead of when the instance needs to be ready. You can't stop instances that have features like failover groups or zone redundancy turned on. | Removes compute and licensing charges during idle periods but retains data and backup storage. Automation ensures consistent savings without manual intervention. |
| Deploy [instance pools](/azure/azure-sql/managed-instance/instance-pools-overview) to host multiple small managed instances that share pre-provisioned compute. [Size the pool](/azure/azure-sql/managed-instance/instance-pools-configure) based on total vCores needed, turn on Azure Hybrid Benefit at the pool level, and combine with reserved capacity for the largest discount.<br><br>Avoid pools for workloads that need the Business Critical tier, zone redundancy, or strict performance isolation. Pooled instances share local disk and network resources, so latency-sensitive workloads with noisy-neighbor risk should deploy as standalone instances. | Unlocks smaller vCore configurations not available outside pools, so you can cost-effectively migrate small SQL Server instances without overprovisioning.<br><br>Shared compute resources reduce per-instance overhead but maintain dedicated vCore and memory isolation for each pooled instance. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to SQL Managed Instance.

> [!div class="checklist"]
>
> - **Implement safe deployment practices for instance configuration changes:** SQL Managed Instance management operations involve virtual cluster infrastructure changes that run much longer than typical database operations. Plan your deployment practices around these extended durations to minimize workload disruption.
>
>   - Account for the extended duration of create, scale, and tier-change operations. Don't schedule them during peak hours. Operations within the same subnet are serialized.
>
>   - Plan rollback and staged deployment strategies that validate configuration changes on nonproduction instances first because scaling operations in either direction take comparable time.
>
>   - Coordinate failover group deployment changes by ensuring that instances have matching update policies. Schedule changes to primary and secondary instances at different times.
>
> - **Define infrastructure as code for virtual network-integrated instance deployments:** SQL Managed Instance requires virtual network-integrated deployment with subnet delegation, NSG rules, and route tables in place before provisioning. Structure your infrastructure as code (IaC) templates in dependency layers so that networking deploys first, identity and RBAC second, and instance resources last.
>
>    Set up configuration drift detection for subnet rules, route tables, and instance properties. Use Azure Policy to enforce deployment standards like required tags, allowed tiers, and zone-redundancy requirements for production instances.
>
> - **Design build and deployment pipelines for database schema and instance changes:** Instance infrastructure changes and database schema deployments operate on fundamentally different timescales. Separate your pipeline stages accordingly. Use status-based polling for infrastructure stages and approval gates before any operation that triggers extended management work.
>
>    Schema changes require validation against documented T-SQL compatibility differences before deployment. Check compatibility in the pipeline and verify cross-database query dependencies and instance-scoped features in post-deployment validation.
>
> - **Establish observability by using Azure Monitor and SQL-native diagnostics:** SQL Managed Instance observability requires both Azure Monitor platform telemetry and SQL-native diagnostics like dynamic management views (DMVs), Query Store, and extended events. Combine these layers for a complete view of instance health and workload performance.
>
>    Set up platform monitoring through Azure Monitor by applying diagnostic settings across the instance. Management operation events appear in the activity log, and Resource Health alerts provide early warning of both planned and unplanned events. On the database side, use Query Store as your primary tool for workload analysis and index optimization. SQL Managed Instance doesn't support automated index tuning.
>
>    Subscribe to Resource Health alerts and advance maintenance notifications for early warning of planned and unplanned events.
>
> - **Automate management, monitoring, and maintenance tasks:** SQL Managed Instance includes SQL Agent, a built-in job scheduler that handles database maintenance and operational tasks without external orchestration. Combine SQL Agent with Azure-level automation for full coverage of both database engine and management plane operations.
>
>   - Use SQL Agent for recurring database maintenance, sign-in synchronization to failover group secondaries, and alerting on job failures.
>
>   - Use Azure Automation or Azure Functions for operations that require Azure Resource Manager interaction, like monitoring management operations and managing backup retention.
>
>   - Automate life cycle operations like stop and start schedules for eligible instances during idle periods and consistent diagnostic configuration when you provision new instances.
>
> - **Establish testing and validation practices for database deployments:** SQL Managed Instance SQL Server compatibility supports standard testing tools, but you still need to validate management operations, cross-database dependencies, and instance-scoped features. Use actual SQL Managed Instance test environments because SQL Database and SQL Server have different infrastructure and management operation behavior.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [Monitor management operation progress](/azure/azure-sql/managed-instance/management-operations-monitor) by using PowerShell, the Azure CLI, or the REST API to track steps and detect problems early. [Cancel](/azure/azure-sql/managed-instance/management-operations-cancel) stalled or unnecessary operations when available. Cancellation itself requires infrastructure cleanup time. | Your team gains visibility into extended operations to make informed decisions about waiting, canceling, or escalating. Early cancellation reduces the impact of problematic operations before they cause prolonged disruption. |
| Deploy SQL Managed Instance [networking prerequisites](/azure/azure-sql/managed-instance/virtual-network-subnet-create-arm-template) as a separate IaC module that completes before instance provisioning begins. Include subnet delegation to `Microsoft.Sql/managedInstances` and mandatory [NSG rules for management traffic](/azure/azure-sql/managed-instance/connectivity-architecture-overview#network-requirements) in the template. | You validate networking prerequisites before instance provisioning starts, which prevents deployment failures from missing delegation or NSG rules. Use separate modules to manage the life cycle of networking and database infrastructure independently. |
| Set pipeline timeouts for SQL Managed Instance infrastructure stages based on [documented operation durations](/azure/azure-sql/managed-instance/management-operations-duration), which vary significantly by operation type.<br><br>Poll the [management operations API](/azure/azure-sql/managed-instance/management-operations-monitor) in a loop step to track progress and detect failures early. Don't rely on fixed sleep timers that can't distinguish stalled operations from long-running ones. | Correct timeouts eliminate false pipeline failures caused by operations that exceed default timeout periods.<br><br>Progress polling within the pipeline helps your team respond faster to genuine failures instead of waiting for arbitrary timers to expire. |
| Turn on instance-scoped [diagnostic settings](/azure/azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure) and stream resource logs for all relevant categories to a Log Analytics workspace. Apply the same streaming export configuration across all instances to establish a reliable observability baseline.<br><br>Add activity log diagnostic settings to capture management operation events. Use virtual cluster health indicators to detect infrastructure-level problems that affect instance availability. | Use KQL queries in Log Analytics to analyze logs across all instances, which supports historical trend analysis for capacity planning and performance troubleshooting.<br><br>Management operation telemetry and virtual cluster health data help you troubleshoot extended operations and subnet-level coordination problems that are specific to SQL Managed Instance. |
| Set up [advance notifications](/azure/azure-sql/managed-instance/advance-notifications) for planned maintenance events to receive alerts before the window opens and when maintenance completes. Use these notifications to trigger preparation workflows like notifying application teams or adjusting retry policies. | Your operations team gets advance preparation time before planned maintenance, which reduces disruption to business-critical workloads and supports proactive communication with stakeholders. |
| Set up activity log alerts for management operations that exceed expected duration thresholds or fail. Failed operations can leave the instance in an intermediate state that requires intervention.<br><br>Use the [management operations API](/azure/azure-sql/managed-instance/management-operations-monitor) or Azure Monitor workbooks to surface operation progress and duration trends across your SQL Managed Instance estate. | Early detection of stalled or failing operations reduces the window of risk during extended infrastructure changes. Your team gets time to intervene before the impact escalates. |
| Create [SQL Agent jobs](/azure/azure-sql/managed-instance/job-automation-managed-instance) for index maintenance, statistics updates, and consistency checks across all databases on the instance. Schedule these jobs during low-traffic periods and stagger execution to avoid resource contention. | Consistent automated maintenance keeps database health and query performance on track without external scheduling tools.<br><br>The SQL Managed Instance native SQL Agent reduces operational overhead compared to custom automation infrastructure. |
| Use SQL database projects with the SQL Managed Instance target platform to validate [T-SQL compatibility](/azure/azure-sql/managed-instance/transact-sql-tsql-differences-sql-server) and identify unsupported syntax before production deployment.<br><br>Test cross-database query dependencies by using three-part naming in integration tests. Verify instance-scoped features like SQL Agent job definitions, Service Broker configurations, and linked servers as part of deployment validation. | You find compatibility problems and deployment failures before they reach production, which reduces rollback risk for operations that take extended time to complete. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for SQL Managed Instance.

> [!div class="checklist"]
>
> - **Conduct capacity planning:** SQL Managed Instance uses an instance-scoped model where all databases share vCores, memory, input/output operations per second (IOPS), and storage. Capacity planning across the database estate is essential because one database's resource consumption directly affects all others.
>
>    Identify performance targets for response time, throughput, and concurrent sessions. Capture performance baselines before migration and project growth patterns for database count, storage, and transaction volumes over the instance life cycle. Factor in tier-specific TempDB constraints when you plan for TempDB-intensive workloads.
>
>    Management operations for scaling take hours rather than the minutes typical of other Azure SQL offerings. Plan capacity with sufficient buffer to avoid urgent scaling needs, and coordinate operations with maintenance windows to minimize business disruption.
>
> - **Select hardware generation and service tier for workload requirements:** Hardware generation and service tier selection determines the memory ratio, I/O characteristics, and latency profile available to your workload.
>
>    | Decision | What to evaluate |
>    |---|---|
>    | Service tier | Match to I/O latency and feature requirements. Account for architecture differences between remote and local storage and the latency effect of zone-redundant configurations. |
>    | IOPS model | Allocation approaches range from file-size-dependent scaling (General Purpose) to instance-level and per-vCore provisioning (Business Critical, Next-gen General Purpose). |
>    | Connection type | Use the Redirect connection type on virtual network-local endpoints to reduce per-query latency for persistent workloads. |
>
> - **Choose the right scaling strategy for your workload:** SQL Managed Instance supports only vertical scaling. The service doesn't offer autoscaling, serverless compute, or a Hyperscale tier.
>
>   - Plan vertical scaling around multihour operation windows for tier changes, hardware generation changes, and vCore adjustments. Schedule these operations during planned maintenance periods.
>
>   - Offload read workloads to the built-in readable secondary replica on the Business Critical tier. This replica serves read-only queries at no extra cost for workloads like reporting, analytics, and eventually consistent reads.
>
>   - Use flexible memory on Next-gen General Purpose to adjust memory allocation independently from vCore count. Changes trigger an instance failover as the final step.
>
> - **Run performance testing to validate database workload behavior:** Design performance tests that validate multidatabase concurrent behavior under the instance-scoped model. Include realistic transaction mixes that combine transactional, batch, and reporting workloads that run simultaneously. Test with the actual database count and data volumes planned for the instance.
>
>    Capture predeployment baselines and compare post-migration behavior across different hardware generations and service tiers to confirm that the selected configuration meets your requirements. Measure workload recovery time after failovers and management operations, particularly on tiers that use remote storage where the buffer pool must rebuild.
>
> - **Identify and monitor performance indicators:** Monitor at both instance and database levels to identify resource contention because all databases share compute resources in the instance-scoped model.
>
>   - Set up instance-level monitoring to track core metrics and set alerts on utilization thresholds before workloads are affected. Include log write throughput against per-tier limits.
>
>   - Use Query Store as your primary tool to identify regressed queries and plan changes. Combine it with manual index analysis because SQL Managed Instance doesn't support automatic index tuning.
>
>   - Add database-level resource monitoring to track per-database consumption and detect contention for shared resources like TempDB and in-memory storage.
>
> - **Optimize workload design and query performance:** SQL Managed Instance includes Enterprise Edition capabilities that address performance challenges specific to the instance-scoped model, like resource governance across databases, cross-database queries, distributed transactions, and in-memory processing.
>
>    Account for service-specific resource constraints in your optimization strategy. Fixed TempDB allocation, log rate governance limits on write throughput, and tier-dependent IOPS models each present both constraints and optimization opportunities.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Select the hardware generation by [memory-per-vCore ratio](/azure/azure-sql/managed-instance/service-tiers-managed-instance-vcore) rather than adding vCores when memory is the constraint. The premium-series hardware generation balances memory and cost, while the memory-optimized premium-series hardware generation suits large buffer pools or in-memory OLTP [capacity limits](/azure/azure-sql/managed-instance/resource-limits). | Rightsizing by memory ratio avoids overprovisioning vCores, which reduces cost and meets performance targets. |
| Set the [Redirect connection type](/azure/azure-sql/managed-instance/connection-types-overview) on the virtual network-local endpoint for workloads that connect from within the virtual network.<br><br>This setting routes traffic directly to the hosting node after initial authentication. Public and private endpoints always use the Proxy connection type regardless of this setting. | Reduces per-query latency for persistent virtual network-local connections by bypassing the gateway after initial authentication. |
| Route read-only workloads to the Business Critical [secondary replica](/azure/azure-sql/database/read-scale-out) by using `ApplicationIntent=ReadOnly` in the connection string. Monitor secondary replica lag to confirm that offloaded workloads meet latency targets.<br><br>Use the failover group geo-secondary for cross-region read offload when available. On SQL Managed Instance, this approach reduces pressure on shared vCores and memory across all databases on the instance. | Frees primary compute for write-intensive operations across all databases by directing reads to the included secondary replica at no extra cost. |
| Turn on [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) in `Read-Write` mode on each database and review reports regularly to identify regressed queries, plan changes, and resource-consuming operations.<br><br>Combine Query Store insights with missing index DMVs to evaluate and create indexes manually. SQL Managed Instance doesn't support automatic index management.<br><br>Set retention and storage-size values to balance historical data availability with storage consumption. | Gives you continuous visibility into query plan quality and runtime behavior so that you can detect regressions early and make informed optimization decisions. |
| Use [in-memory OLTP](/azure/azure-sql/managed-instance/in-memory-oltp-overview) on the Business Critical tier for high-throughput workloads like data ingestion, caching, and session state. Factor in available in-memory OLTP [storage capacity](/azure/azure-sql/managed-instance/in-memory-oltp-monitor-space) when you select your hardware generation because limits vary by configuration. | Eliminates latch contention and reduces CPU overhead for high-throughput workloads, which often removes the need for a higher compute tier. |
| Set up [resource governor](/sql/relational-databases/resource-governor/resource-governor) to control CPU, memory, and I/O allocation across workloads that share the instance and account for [SQL Managed Instance-specific behavior](/azure/azure-sql/managed-instance/transact-sql-tsql-differences-sql-server#resource-governor). This capability directly addresses the noisy-neighbor risk in the instance-scoped deployment model. | Prevents any single database or query pattern from monopolizing shared instance resources, which protects workload isolation across the database estate. |
| Plan TempDB usage around the fixed allocation and file configuration, which you can't modify on SQL Managed Instance.<br><br>The General Purpose tier provides a lower [per-vCore TempDB allocation](/azure/azure-sql/managed-instance/resource-limits#service-tier-characteristics-comparison) than other Azure SQL offerings. Evaluate the Business Critical tier for TempDB-intensive workloads that exceed these constraints. | Avoids TempDB space exhaustion by factoring in the fixed allocation and file configuration constraints on the General Purpose tier. |
| Optimize [data file sizes](/azure/azure-sql/managed-instance/resource-limits#service-tier-characteristics-comparison) in the General Purpose tier to maximize IOPS. Larger files receive proportionally higher throughput from the remote storage layer.<br><br>In Next-gen General Purpose, IOPS scale at the instance level based on reserved storage rather than per file. | Improves read and write throughput by adjusting file sizes without requiring a tier upgrade. |

## Azure policies

Azure provides an extensive set of built-in policies related to SQL Managed Instance and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- You should use customer-managed keys to encrypt data at rest on SQL managed instances.
- You should activate Microsoft Entra-only authentication on SQL managed instances.
- You should block public network access on SQL managed instances.
- You should avoid GRS backup redundancy on SQL managed instances.

For comprehensive governance, review the [Azure Policy built-in definitions for SQL Managed Instance](/azure/governance/policy/samples/built-in-policies#sql) and other policies that might affect the security of your database workload.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Fully managed and secured app on SQL Managed Instance](/azure/architecture/example-scenario/apps/fully-managed-secure-apps).

## Related content

- [SQL Database service guide](/azure/well-architected/service-guides/azure-sql-database)
- [Data store decision tree](/azure/architecture/guide/technology-choices/data-store-decision-tree)
- [Well-Architected Framework pillars](/azure/well-architected/pillars)
