---
title: Architecture Best Practices for Azure Database for PostgreSQL
description: Design considerations and recommendations for each pillar as related to solutions using Azure Database for PostgreSQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.reviewer: maghan
ms.date: 05/07/2025
ms.service: azure-waf
ms.subservice: waf-service-guide
ms.topic: conceptual
products:
  - azure-database-postgresql
azure.category:
  - databases
---

# Architecture best practices for Azure Database for PostgreSQL

[Azure Database for PostgreSQL](/azure/PostgreSQL/overview) is a relational database service in Azure based on the PostgreSQL open-source relational database. It's a fully managed database as a service offering that can handle mission-critical workloads with predictable performance, security, high availability, and dynamic scalability. Azure Database for PostgreSQL is built on the community edition of the PostgreSQL database engine. It's compatible with the PostgreSQL server community edition and supports PostgreSQL extension features such as PostGIS and TimescaleDB.

This article assumes that as an architect, you've reviewed the [Azure data options](/azure/architecture/guide/technology-choices/data-options) and chose Azure Database for PostgreSQL as the data store for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
>**How to use this guide**
>
>Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
>Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Azure Database for PostgreSQL and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
>Foundational architecture that demonstrates the key recommendations:
>[Reliable Web App pattern for Java](/azure/architecture/web-apps/guides/enterprise-app-patterns/reliable-web-app/java/guidance)

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Database for PostgreSQL

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](../reliability/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>
> - **Familiarize yourself with Azure Database for PostgreSQL product reliability guidance:**
>   For more information, see the following resources:
>   - [Busines continuity overview](/azure/postgresql/flexible-server/concepts-business-continuity)
>   - [Reliability and high availability](/azure/reliability/reliability-postgresql-flexible-server)
>   - [Backup and restore](/azure/postgresql/flexible-server/concepts-backup-restore)
>   - [Geo-disaster recovery](/azure/postgresql/flexible-server/concepts-geo-disaster-recovery)
> - **Align your reliability and recovery targets for Azure Database for PostgreSQL with your workload targets.** Ensure that you choose an appropriate Azure Database for PostgreSQL SKU that can support your reliability and recovery targets.
> - **Determine the appropriate high availability and redundancy configurations for your Azure Database for PostgreSQL instances.** Determine whether you need zone-redundant or zonal redundancy to meet your reliability requirements.
> - **Incorporate your Azure Database for PostgreSQL instances into your disaster recovery (DR) planning and exercises.** Include the recovery of your databases in your DR planning and exercises to ensure that your entire workload is recoverable according to your recovery targets.
> - **Incorporate your Azure Database for PostgreSQL instances into your observability platform.** Enable reliablity metrics like replication to monitor the health of your instance. Include [high availability health monitoring](/azure/postgresql/flexible-server/how-to-monitor-high-availability) in your monitoring solution.

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| Select the appropriate [high availability configuration](/azure/postgresql/flexible-server/how-to-configure-high-availability). | Azure Database for PostgreSQL Server offers high availability configurations, ensuring that the service remains available if there's a zone outage and no data is lost. When high availability is configured, the Azure Database for PostgreSQL server automatically provisions and manages a standby replica. |
| [Configure geo-redundant backup](/azure/postgresql/flexible-server/concepts-geo-disaster-recovery#geo-redundant-backup-and-restore). | Cross-region read replicas can be deployed to protect your databases from region-level failures. Geo Redundant backups are enabled in selected regions and help with disaster recovery if the primary server region is down. <br><br> Geo-redundancy can also be achieved by using an Azure Backup vault for long-term storage of the recovery points. In case of a regional outage or disaster, you can use Azure Backup to restore the database server to an Azure-paired region, minimizing downtime. <br><br> Azure Backup also offers geo-redundancy for Azure Database for PostgreSQL, increasing efficiency and reducing downtime during disasters or region outages.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Azure Database for PostgreSQL.

### Design checklist

> [!div class="checklist"]
> 
> - **Review the security baseline**: To enhance the security posture of your workload, review the [Azure security baseline for Azure Database for PostgreSQL](/security/benchmark/azure/baselines/azure-database-for-postgresql-flexible-server-security-baseline).
> - **Implement strict, conditional, and auditable identity and access management**: Use Microsoft Entra ID for authentication and authorization to enhance identity management.
> - **Apply network segmentation and security controls**: Use built-in firewalling at the server level alongside virtual network firewalling mechanisms to isolate your instance from other workload components and public networks.
> - **Use native security features to add additional layers of protection**: Help prevent malicious activities by configuring row-level security and connection throttling.

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| Implement [network security groups](/azure/postgresql/flexible-server/concepts-security#network-security) and [firewalls](/azure/postgresql/flexible-server/concepts-firewall-rules) to control access to your database. | As part of the Zero Trust Model for security, network segmentation is recommended where communication paths between components (in this case, application and database server) are restricted to only what's needed. This can be implemented using Network Security Group and Application Security Groups. |
| [Connect to your databases over Azure Private Link](/azure/postgresql/flexible-server/concepts-networking-private-link) | Azure Private Link allows you to create private endpoints for Azure Database for PostgreSQL  to bring it inside your virtual network. |
| [Use Microsoft Entra ID](/azure/postgresql/flexible-server/how-to-configure-sign-in-azure-ad-authentication) for authentication and authorization to enhance identity management. | Microsoft Entra ID authentication is a mechanism of connecting to Azure Database for PostgreSQL using identities defined in Azure AD. |
| Configure [row-level security](/azure/postgresql/flexible-server/concepts-security#row-level-security). | Row level security (RLS) is a PostgreSQL security feature that allows database administrators to define policies to control how specific rows of data display and operate for one or more roles. Row-level security is an additional filter you can apply to a PostgreSQL database table. |
| When required for compliance reasons, [use customer managed keys (CMK)](/azure/postgresql/flexible-server/concepts-data-encryption#recommendations) for data encryption, and store your keys in Azure Key Vault. | CMK allows you full control of your encryption key's life cycle, including rotation of the key, to align with corporate policies. Storing your keys in Azure Key Vault allows you to centrally manage and organize all your encryption keys in your own instances of Azure Key Vault. |
| Enable connection throttling for IPs with too many failed login attempts. | Setting the connection_throttling server parameter to enabled protects your databases from malicious login attempts and potential Distributed Denial of Service (DDoS) attacks by throttling connections from IP addresses that have repeated failed logins. |

## Cost optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Database for PostgreSQL and its environment.


### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - **Estimate the initial cost**: As part of your cost modeling exercise, use the Azure pricing calculator to evaluate the approximate costs associated with Azure Database for PostgreSQL in your workload.
> - **Choose the right service tier for your workload**: Determine whether Burstable, General Purpose, or Memory Optimized compute resources meet your use case needs.
> - **Apply a well-informed scaling strategy**: Continuously observe and analyze your workload capacity and demand to determine when and how to scale your Azure Database for PostgreSQL resources.
> - **Take advantage of available discounts**: Consider 1 or 3 year compute reservations. Reserved instances can save you significant costs for compute resources.
> - **Use your provisioned storage**: There's no extra charge for backup storage up to 100% of your total provisioned server storage.
> - **Understand redundancy costs**: Using zone-redundant or zonal redundancy effectively doubles your instance costs, so carefully consider your redundancy design.
> - **Understand protected instance and backup storage cost**: When backing up PostgreSQL using Azure Backup, you incur Protected Instance Fees (per 250 GB) and Backup Storage Fees (based on total data stored and redundancy type).
> - **Deploy to the same region as an app**: Deploy to the same region as the application(s) to minimize transfer costs.
> - **Consolidate databases and servers**: When practical, consolidating multiple databases and servers into a single server can help reduce costs.

### Recommendations

| Recommendations | Benefits |
| --- | --- |
| Pick the right [tier and SKU](/azure/postgresql/flexible-server/concepts-compute). | Choosing the right tier and SKU helps you avoid wasting money on over-provisioned resources. Azure Advisor gives you recommendations to optimize and reduce your overall Azure spending. Recommendations include server right-sizing that you should follow. |
| [Scale compute and storage resources](/azure/postgresql/flexible-server/concepts-scaling-resources) when your workload demand changes. | You can scale compute resources vertically and horizontally, and you can scale up and down as necessary. Be aware that you cannot scale storage down after scaling up. |
| Use the Start/Stop feature. | The  has a Start/Stop feature that you can use to stop the server from running when you don't need it. |

## Operational excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - **Optimize the recoverablility of your databases**: Define backup and retention policies to meet your compliance requirements.
> - **Automate operational tasks**: Use [automation tasks](/azure/postgresql/flexible-server/create-automation-tasks) to automatically perform tasks like starting and stopping a server, scaling resources, and others.
> - **Monitor database health and performance:** Collect and analyze logs and metrics from your instance to proactively detect potential or active issues.

### Recommendations

| Recommendation | Benefits |
| --- | --- |
| Use an [Azure Backup policy](/azure/backup/backup-azure-database-postgresql-overview) indvidually or together with the native automated backups to back up databases. | Azure Database for PostgreSQL provides automated backups and point-in-time restore for your database. You can configure the retention period for backups up to 35 days.  <br><br> Azure Backup allows you to define how and when backups are created, the retention period for recovery points, and the rules for data protection and recovery. You can retain recovery points in a Backup vault for up to **10 years**. You can also manage the backup and restore operations using [Azure Business Continuity Center](/azure/business-continuity-center/business-continuity-center-overview).|
| Schedule [custom maintenance windows](/azure/postgresql/flexible-server/how-to-configure-scheduled-maintenance) to apply service updates. | You can define a custom schedule for each Azure Database for PostgreSQL  in your Azure subscription. With a custom schedule, you can specify your maintenance window for the server, by choosing the day of the week and the start time of a one hour time window. |
| [Monitor your server](/azure/postgresql/flexible-server/concepts-monitoring) to ensure it's healthy and performing as expected. | Azure Database for PostgreSQL  provides various metrics that give insight into the behavior of the resources that support the Azure Database for PostgreSQL  instance. You can also enable enhanced metrics to get fine-grained monitoring and alerting on databases. |

## Performance efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - **Optimize queries**: Use native features to find opportunities to optimize queries.
> - **Optimize your index**: Use native features to optimize your database index.
> - **Fine-tune your database**: Use automatic tuning features to optimize performance.
> - **Offload read-only operations**: If your application supports read-only connection strings, you can offload read-only operations to read replicas.

### Recommendations

| Recommendation | Benefits |
| --- | --- |
| Use [Query store](/azure/postgresql/flexible-server/concepts-query-store) to track query performance over time. | Query store simplifies the troubleshooting of performance issues by helping you quickly find the longest running and most resource-intensive queries. |
| Use [Query Performance Insights](/azure/postgresql/flexible-server/concepts-query-performance-insight) to identify the top resource consuming and long-running queries in your workload. | Using Query Performance Insights helps you identify long-running queries and their changes over time, determine the wait types affecting them, analyze top database queries by execution count, and other benefits. |
| Use [index tuning](/azure/postgresql/flexible-server/concepts-index-tuning) in conjunction with query store to analyze tracked queries and provide recommendations. | Index tuning analyzes the workload tracked by query store, and produces index recommendations to improve the performance of the analyzed workload or to drop duplicate or unused indexes. It helps you identify beneficial indexes, duplicate indexes, and unused indexes. |
| Use [intelligent tuning](/azure/postgresql/flexible-server/concepts-intelligent-tuning) to enhance performance automatically and help prevent problems. | Intelligent tuning continuously monitors the Azure Database for PostgreSQL flexible server database's status and dynamically adapts the database to your workload. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Database for PostgreSQL and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- [Geo-redundant backup is enabled](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee2f9fd-3968-44be-a863-bd62c9884423)

- [Connection throttling is enabled](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdacf07fa-0eea-4486-80bc-b93fae88ac40)

- [SSL connections are enforced](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc29c38cb-74a7-4505-9a06-e588ab86620a)

- [Data at rest is encryted with CMK](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12c74c95-0efd-48da-b8d9-2a7d68470c92)

- [Private endpoints are enabled](https://ms.portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5375a5bb-22c6-46d7-8a43-83417cfb4460)

## Related resources

### Azure Architecture Center Guidance

- [Multitenancy and Azure Database for PostgreSQL](/azure/architecture/guide/multitenant/service/postgresql)
- [Best practices](https://azure.microsoft.com/blog/performance-best-practices-for-using-azure-database-for-postgresql/)
- [Optimize performance](https://azure.microsoft.com/blog/optimize-performance-using-azure-database-for-postgresql-recommendations/)
- [Tuning](https://azure.microsoft.com/blog/performance-updates-and-tuning-best-practices-for-using-azure-database-for-postgresql/)

### Cloud Adoption Framework guidance

- [Batch Data application with Azure Database for PostgreSQL](/azure/cloud-adoption-framework/scenarios/cloud-scale-analytics/architectures/data-landing-zone-data-products#batch-data-application)
