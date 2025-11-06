---
title: Architecture Best Practices for Azure Database for PostgreSQL
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Database for PostgreSQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.reviewer: maghan
ms.date: 10/13/2025
ms.service: azure-waf
ms.subservice: waf-service-guide
ms.topic: concept-article
products:
  - azure-database-postgresql
azure.category:
  - databases
---

# Architecture best practices for Azure Database for PostgreSQL

Azure Database for PostgreSQL is a relational database service in Azure that's based on the PostgreSQL open-source relational database. It's a fully managed, cloud-based database solution that supports mission-critical workloads with predictable performance, security, high availability, and dynamic scalability. Azure Database for PostgreSQL is built on the community edition of the PostgreSQL database engine. It's compatible with the PostgreSQL server community edition and supports PostgreSQL extension features, such as PostGIS and TimescaleDB.

This article assumes that as an architect, you've reviewed the [Azure data options](/azure/architecture/guide/technology-choices/data-options) and chose Azure Database for PostgreSQL as the data store for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Database for PostgreSQL

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](../reliability/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

> [!div class="checklist"]
>
> - **Familiarize yourself with Azure Database for PostgreSQL product reliability guidance.**  
>   For more information, see the following resources:
>   - [Business continuity overview](/azure/postgresql/flexible-server/concepts-business-continuity)
>   - [Reliability and high availability](/azure/reliability/reliability-postgresql-flexible-server)
>   - [Backup and restore](/azure/postgresql/flexible-server/concepts-backup-restore)
>   - [Geo-disaster recovery](/azure/postgresql/flexible-server/concepts-geo-disaster-recovery)
>
> - **Align your reliability and recovery targets for Azure Database for PostgreSQL with your workload targets.** Choose an appropriate Azure Database for PostgreSQL SKU that can support your reliability and recovery targets.
> - **Determine the appropriate high availability and redundancy configurations for your Azure Database for PostgreSQL instances.** Determine whether you need zone-redundant or zonal configurations to meet your reliability requirements.
> - **Incorporate the recovery of your Azure Database for PostgreSQL instances into your disaster recovery planning and exercises.** Ensure that your entire workload is recoverable according to your recovery targets.
> - **Incorporate your Azure Database for PostgreSQL instances into your observability platform.** Enable enhanced metrics to monitor the health of your instance. Include [high availability health monitoring](/azure/postgresql/flexible-server/how-to-monitor-high-availability) in your monitoring solution.

### Configuration recommendations

| Recommendation | Benefit |
| --- | --- |
| Select the appropriate [high availability configuration](/azure/postgresql/flexible-server/how-to-configure-high-availability). | When high availability is configured, the Azure Database for PostgreSQL server automatically provisions and manages a standby replica. This setup ensures that the service remains available and doesn't lose data during a zone outage.  |
| Configure [geo-redundant backups](/azure/postgresql/flexible-server/concepts-backup-restore#geo-redundant-backup-and-restore). | Cross-region read replicas can be deployed to protect your databases from region-level failures. Geo-redundant backups are enabled in selected regions and help with disaster recovery if the primary server region is unavailable. <br><br> Geo-redundancy can also be achieved by using an Azure Backup vault for long-term storage of the recovery points. If a regional outage or disaster occurs, you can use Backup to restore the database server to an Azure-paired region, which minimizes downtime. <br><br> Azure Backup also provides geo-redundancy for Azure Database for PostgreSQL. This feature increases efficiency and reduces downtime during disasters or regional outages.|
| Test your backup and restore strategy regularly. | Regularly testing your backup and restore strategy ensures that you can recover your databases and maintain operations if a failure occurs. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Database for PostgreSQL.

### Workload design checklist

> [!div class="checklist"]
> 
> - **Review the security baseline.** To enhance the security posture of your workload, review the [Azure security baseline for Azure Database for PostgreSQL](/security/benchmark/azure/baselines/azure-database-for-postgresql-flexible-server-security-baseline).
>
> - **Implement strict, conditional, and auditable identity and access management.** Use Microsoft Entra ID for authentication and authorization to enhance identity management.
> - **Apply network segmentation and security controls.** Use built-in firewalling at the server level alongside virtual network firewalling mechanisms to isolate your instance from other workload components and public networks.
> - **Use native security features to add extra layers of protection.** Help prevent malicious activities by configuring row-level security and connection throttling.

### Configuration recommendations

| Recommendation | Benefit |
| --- | --- |
| Implement [network security groups](/azure/postgresql/flexible-server/concepts-security#network-security) and [firewalls](/azure/postgresql/flexible-server/concepts-firewall-rules) to control access to your database. | As part of the Zero Trust model for security, use network segmentation to restrict communication paths between components, such as application and database servers, to only what's needed. This feature can be implemented by using network security groups and application security groups. |
| Connect to your databases over [Azure Private Link](/azure/postgresql/flexible-server/concepts-networking-private-link). | Private Link allows you to create private endpoints for Azure Database for PostgreSQL  to extend the service inside your virtual network. |
| Use [Microsoft Entra ID](/azure/postgresql/flexible-server/how-to-configure-sign-in-azure-ad-authentication) for authentication and authorization to enhance identity management. | You can use Microsoft Entra authentication to connect to Azure Database for PostgreSQL by using managed identities in Microsoft Entra. |
| Configure [row-level security](/azure/postgresql/flexible-server/concepts-security#row-level-security). | Row-level security is a PostgreSQL security feature that allows database administrators to define policies to control how specific rows of data display and operate for one or more roles. Row-level security is an extra filter that you can apply to a PostgreSQL database table. |
| If needed for compliance, [use customer managed keys (CMKs)](/azure/postgresql/flexible-server/concepts-data-encryption#recommendations) for data encryption, and store your keys in Azure Key Vault. | CMKs give you full control of your encryption key's life cycle, including key rotation, to align with corporate policies. Key Vault enables centralized management and organization of your encryption keys within your own dedicated Key Vault instances. |
| For the highest security requirements, enable [confidential computing](/azure/postgresql/flexible-server/concepts-confidential-computing) to protect data in use with hardware-based encryption. | Confidential computing provides hardware-based encryption for data processing operations. Organizations processing sensitive data in highly regulated industries can meet stringent regulatory requirements while maintaining database performance. |
| Enable connection throttling for IP addresses that have excessive failed sign-in attempts. | Setting the `connection_throttling` server parameter to `enabled` helps protect your databases from malicious sign-in attempts and distributed denial of service (DDoS) attacks by limiting repeated connection attempts from the same IP address. |
| Conduct security audits regularly. | Regularly conducting security audits helps identify and remediate potential vulnerabilities. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Database for PostgreSQL and its environment.

### Workload design checklist

> [!div class="checklist"]
>  
> - **Estimate the initial cost.** As part of your cost modeling exercise, use the Azure pricing calculator to evaluate the approximate costs associated with Azure Database for PostgreSQL in your workload.
>
> - **Choose the right service tier for your workload.** Determine whether the Burstable, General Purpose, or Memory Optimized pricing tier for compute resources meets your use case needs.
> - **Apply a well-informed scaling strategy.** To determine when and how to scale your Azure Database for PostgreSQL resources, continuously observe and analyze your workload capacity and demand.
> - **Take advantage of available discounts.** Consider one or three year compute reservations. Reserved instances can save you significant costs for compute resources.
> - **Use your provisioned storage.** There's no extra charge for backup storage up to 100% of your total provisioned server storage.
> - **Understand redundancy costs.** Using zone-redundant or zonal configurations doubles your instance costs, so carefully consider your redundancy design.
> - **Understand protected instance and backup storage costs.** When you back up Azure Database for PostgreSQL by using Azure Backup, you incur protected instance fees for every 250 GB and backup storage fees based on total data stored and redundancy type.
> - **Deploy to the same region as an app.** Deploy to the same region as your applications to minimize transfer costs.
> - **Consolidate databases and servers.** When practical, consolidating multiple databases and servers into a single server can help reduce costs.

### Configuration recommendations

| Recommendations | Benefits |
| --- | --- |
| Pick the right [tier and SKU](/azure/postgresql/flexible-server/concepts-compute). | Choosing the right tier and SKU helps you avoid wasting money on over-provisioned resources. Azure Advisor provides recommendations, include server rightsizing suggestions, to optimize and reduce your overall Azure spending. |
| [Scale compute and storage resources](/azure/postgresql/flexible-server/concepts-scaling-resources) when your workload demand changes. | You can scale compute resources vertically (up or down) and horizontally (out or in) as necessary. You can't scale storage down after scaling up. |
| Use the start/stop feature. | Use the start/stop feature to stop the server from running when it isn't needed. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

> [!div class="checklist"]
>  
> - **Optimize the recoverability of your databases.** Define backup and retention policies to meet your compliance requirements.
>
> - **Automate operational tasks.** Use [automation tasks](/azure/postgresql/flexible-server/create-automation-tasks) to automatically perform tasks like starting and stopping a server, scaling resources, and other tasks.
>
> - **Monitor database health and performance.** Collect and analyze logs and metrics from your instance to proactively detect potential problems.
>
> - **Stay current with PostgreSQL versions.** Latest versions come with in-place upgrade capabilities with zero-downtime, enabling major version transitions without service disruption.

### Configuration recommendations

| Recommendation | Benefits |
| --- | --- |
| Use an [Azure Backup policy](/azure/backup/backup-azure-database-postgresql-overview) individually or together with the native automated backups to back up databases. | Azure Database for PostgreSQL provides automated backups and point-in-time restore for your database. You can configure the retention period for backups up to 35 days. <br><br> Use Azure Backup to define how and when backups are created, the retention period for recovery points, and the rules for data protection and recovery. You can retain recovery points in an Azure Backup vault for up to 10 years. You can also manage the backup and restore operations by using [Azure Business Continuity Center](/azure/business-continuity-center/business-continuity-center-overview).|
| Schedule [custom maintenance windows](/azure/postgresql/flexible-server/how-to-configure-scheduled-maintenance) to apply service updates. | You can define a custom schedule for each Azure Database for PostgreSQL in your Azure subscription. With a custom schedule, you can specify your maintenance window for the server by choosing the day of the week and the start time of the one-hour maintenance window. |
| [Monitor your server](/azure/postgresql/flexible-server/concepts-monitoring) to ensure that it's healthy and performing as expected. | Azure Database for PostgreSQL has various metrics that provide insight into the behavior of the resources that support the Azure Database for PostgreSQL instance. You can also enable enhanced metrics to get fine-grained monitoring and alerting on databases. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

> [!div class="checklist"]
>  
> - **Optimize queries.** Use native features to find opportunities to optimize queries.
>
> - **Optimize indexes.** Use features such as index tuning to automatically analyze query patterns and receive actionable recommendations for creating or dropping indexes to improve performance.
> - **Offload read-only operations.** If your application supports read-only connection strings, you can offload read-only operations to read replicas.

### Configuration recommendations

| Recommendation | Benefits |
| --- | --- |
| Use [query store](/azure/postgresql/flexible-server/concepts-query-store) to track query performance over time. | Query store simplifies troubleshooting performance problems by helping you quickly find the longest running and most resource-intensive queries. |
| Use [Query Performance Insight](/azure/postgresql/flexible-server/concepts-query-performance-insight) to identify the top resource consuming and long-running queries in your workload. | Query Performance Insight helps you identify long-running queries and their changes over time, determine the wait types that affect them, analyze top database queries by frequency, and other benefits. |
| Use [index tuning](/azure/postgresql/flexible-server/concepts-index-tuning) with query store to analyze tracked queries and provide recommendations. | Index tuning analyzes the workload that query store tracks. It produces index recommendations to improve the performance of the analyzed workload or to drop duplicate or unused indexes. Index tuning helps you identify beneficial indexes, duplicate indexes, and unused indexes. |
| Use [intelligent tuning](/azure/postgresql/flexible-server/concepts-intelligent-tuning) to enhance performance automatically and help prevent problems. | Intelligent tuning continuously monitors the Azure Database for PostgreSQL flexible server database's status and dynamically adapts the database to your workload. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Database for PostgreSQL and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- [Geo-redundant backup is enabled](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee2f9fd-3968-44be-a863-bd62c9884423).

- [Connection throttling is enabled](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdacf07fa-0eea-4486-80bc-b93fae88ac40).

- [Secure Sockets Layer (SSL) connections are enforced](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc29c38cb-74a7-4505-9a06-e588ab86620a).

- [Data at rest is encrypted with CMKs](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12c74c95-0efd-48da-b8d9-2a7d68470c92).

- [Private endpoints are enabled](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5375a5bb-22c6-46d7-8a43-83417cfb4460).

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Database for PostgreSQL](/azure/governance/policy/samples/built-in-policies#sql) and other policies that might affect the security of the data stores.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Reliable Web App pattern for Java](/azure/architecture/web-apps/guides/enterprise-app-patterns/reliable-web-app/java/guidance).

## Related content

- [Multitenancy and Azure Database for PostgreSQL](/azure/architecture/guide/multitenant/service/postgresql)

<!-- Updated: October 13, 2025 for Azure Update 500795 -->
