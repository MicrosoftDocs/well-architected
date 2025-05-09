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
| [Define targets](/azure/cloud-adoption-framework/manage/protect#define-reliability-requirements) for RPO (Recovery Point Objective) and RTO (Recovery Time Objective) for workloads. | Derive these values by conducting a risk assessment and ensuring you understand the cost and risk of downtime and data loss. These are nonfunctional requirements of a system and should be dictated by business requirements. |
| Select the appropriate [high availability configuration](/azure/postgresql/flexible-server/how-to-configure-high-availability). | Azure Database for PostgreSQL Server offers high availability configurations, ensuring that the service remains available if there's a zone outage and no data is lost. When high availability is configured, the Azure Database for PostgreSQL server automatically provisions and manages a standby replica. |
| [Configure geo-redundancy backup](/azure/postgresql/flexible-server/concepts-geo-disaster-recovery#geo-redundant-backup-and-restore). | Cross-region read replicas can be deployed to protect your databases from region-level failures. Geo Redundant backups are enabled in selected regions and help with disaster recovery if the primary server region is down. <br><br> Geo-redundancy can also be achieved by using an Azure Backup vault for long-term storage of the recovery points. In case of a regional outage or disaster, you can use Azure Backup to restore the database server to an Azure-paired region, minimizing downtime. <br><br> Azure Backup also offers geo-redundancy for Azure Database for PostgreSQL - Flexible Server, increasing efficiency and reducing downtime during disasters or region outages.|
| Test your disaster recovery plan to ensure rapid data restoration if there's a failure. | Read replicas can be deployed on a different region and promoted to a read-write server if disaster recovery is needed. |
| [Monitor your server](/azure/postgresql/flexible-server/concepts-monitoring) to ensure it's healthy and performing as expected. | Azure Database for PostgreSQL flexible server provides various metrics that give insight into the behavior of the resources that support the Azure Database for PostgreSQL flexible server instance. You can also enable enhanced metrics to get fine-grained monitoring and alerting on databases. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Cosmos DB for NoSQL.

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
| [Connect to your databases over Azure Private Link](/azure/postgresql/flexible-server/concepts-networking-private-link) | Azure Private Link allows you to create private endpoints for Azure Database for PostgreSQL flexible server to bring it inside your virtual network. |
| [Use Microsoft Entra ID](/azure/postgresql/flexible-server/how-to-configure-sign-in-azure-ad-authentication) for authentication and authorization to enhance identity management. | Microsoft Entra ID authentication is a mechanism of connecting to Azure Database for PostgreSQL using identities defined in Azure AD. |
| Configure [row-level security](/azure/postgresql/flexible-server/concepts-security#row-level-security). | Row level security (RLS) is a PostgreSQL security feature that allows database administrators to define policies to control how specific rows of data display and operate for one or more roles. Row-level security is an additional filter you can apply to a PostgreSQL database table. |
| When required for compliance reasons, [use customer managed keys (CMK)](/azure/postgresql/flexible-server/concepts-data-encryption#recommendations) for data encryption, and store your keys in Azure Key Vault. | CMK allows you full control of your encryption key's life cycle, including rotation of the key, to align with corporate policies. Storing your keys in Azure Key Vault allows you to centrally manage and organize all your encryption keys in your own instances of Azure Key Vault. |
| Enable connection throttling for IPs with too many failed login attempts. | Setting the connection_throttling server parameter to enabled protects your databases from malicious login attempts and potential Distributed Denial of Service (DDoS) attacks by throttling connections from IP addresses that have repeated failed logins. |

## Cost optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Cosmos DB for No SQL and its environment.


### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - Pick the right tier and SKU.
> - Understand high availability mode.
> - Scale compute and storage tiers.
> - Consider reserved instances.
> - Use your provisioned storage.
> - Understand geo-redundancy costs.
> - Evaluate storage scale-up decisions.
> - Deploy to the same region as an app.
> - High availability oriented cost description.
> - Consolidate databases and servers.

### Recommendations

| Recommendations | Benefits |
| --- | --- |
| Pick the right [tier and SKU](/azure/postgresql/flexible-server/concepts-compute). | Pick the pricing tier and compute SKUs that support the specific needs of your workload. Azure Advisor gives you recommendations to optimize and reduce your overall Azure spending. Recommendations include server right-sizing that you should follow. |
| Understand [high availability mode](/azure/reliability/reliability-postgresql-flexible-server). | High availability makes a standby server always available within the same zone or region. Enabling high availability doubles your cost. |
| Adjust [compute](/azure/postgresql/flexible-server/concepts-compute) and [storage](/azure/postgresql/flexible-server/concepts-storage) tiers. | You should manually adjust the compute and storage tiers to meet the application's requirements over time. |
| Use the Start/Stop feature. | The Flexible server has a Start/Stop feature that you can use to stop the server from running when you don't need it. |
| Consider [reserved instances](/azure/postgresql/flexible-server/concepts-reserved-pricing). | Consider a one or three-year reservation to receive significant discounts on computing. Use these reservations for workloads with consistent compute usage for a year or more. |
| Use your provisioned storage. | There's no extra charge for backup storage up to 100% of your total provisioned server storage. |
| Understand redundancy costs. | Geo-redundant storage (GRS) costs twice as much as local redundant storage (LRS). GRS requires double the storage capacity of LRS. |
| Understand protected instance and backup storage cost | When backing up PostgreSQL - Flexible Server using Azure Backup, you incur Protected Instance Fees (per 250 GB) and Backup Storage Fees (based on total data stored and redundancy type). | 
| Evaluate storage scale-up decisions. | You should evaluate your current and future storage needs before scaling up your storage. After you scale up storage, you can't scale down. |
| Deploy to the same region as the app. | Deploy to the same region as the application(s) to minimize transfer costs. When you use virtual network integration, applications in a different virtual network don't have direct access to flexible servers. To grant them access, you need to configure virtual network peering. Virtual network peering has nominal inbound and outbound data transfer costs. |
| High availability oriented cost description. | It's a trade-off of HA and costs. HA is double the cost for non-HA configuration, but it's needed. |
| Consolidate databases and servers. | You can consolidate multiple databases and servers into a single server to reduce costs. |

## Operational excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - Set up automated backups and retention policies to maintain data availability and meet compliance requirements.
> - Implement automated patching and updates to keep your PostgreSQL instance secure and up-to-date.
> - Monitor database health and performance using Azure Monitor and set up alerts for critical metrics.

### Recommendations

| Recommendation | Benefits |
| --- | --- |
| Set up automated backups and retention policies to maintain data availability and meet compliance requirements. <br><br> Alternatively, use an [Azure Backup policy](/azure/backup/backup-azure-database-postgresql-overview) to back up Azure Database for PostgreSQL Server. <br><br> You can also use this Azure Backup policy to protect Azure Database for PostgreSQL - Flexible Server.| Azure Database for PostgreSQL provides automated backups and point-in-time restore for your database. You can configure the retention period for backups up to 35 days.  <br><br> Azure Backup allows you to define how and when backups are created, the retention period for recovery points, and the rules for data protection and recovery. You can retain recovery points in a Backup vault for up to **10 years**. <br><br> Use [Azure Backup policy](/azure/backup/backup-azure-database-postgresql-flex-overview) for long term protection of Azure Database for PostgreSQL - Flexible Server and to manage the safety and integrity of the database. You can also manage the backup and restore operations using [Azure Business Continuity Center](/azure/business-continuity-center/business-continuity-center-overview).|
| Implement automated patching and updates to keep your PostgreSQL instance secure and up-to-date. | Azure Database for PostgreSQL provides automated patching and updates for your database. You can configure the maintenance window for your server to minimize the impact on your workload. |
| [Monitor](/azure/postgresql/flexible-server/concepts-monitoring) database health and performance using Azure Monitor and set up alerts for critical metrics. | Azure Database for PostgreSQL provides built-in monitoring and alerting capabilities. You can monitor the health and performance of your database using Azure Monitor. You can also set up alerts for critical metrics to get notified when your database isn't performing as expected. |




## Performance efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - Design your schema and queries for efficiency to minimize resource consumption.
> - Implement read replicas to offload read traffic and enhance overall performance.

### Recommendations

| Recommendation | Benefits |
| --- | --- |
| Design your schema and queries for efficiency to minimize resource consumption. | You should design your schema and queries for efficiency to minimize resource consumption. |
| Implement [read replicas](/azure/postgresql/flexible-server/concepts-read-replicas) to offload read traffic and enhance overall performance. | You can use read replicas to offload read traffic and enhance performance. |

### Performance efficiency policy definitions

Azure Policy definitions help you enforce specific rules and configurations for resources within your Azure environment. To ensure Performance efficiency for Azure Database for PostgreSQL, you can create custom Azure Policy definitions to enforce specific configurations and best practices. Here's an example of some custom Azure Policy definitions you can create for Performance efficiency:

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

## Next step

> [!div class="nextstepaction"]
> [Azure pricing calculator to estimate and manage your costs effectively](https://azure.microsoft.com/pricing/calculator/)
