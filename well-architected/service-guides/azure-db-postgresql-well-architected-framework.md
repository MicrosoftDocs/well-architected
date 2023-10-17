---
title: Microsoft Azure Well-Architected Framework review for Azure Database for PostgreSQL
description: Provides a template for a Well-Architected Framework (WAF) article that is specific for Azure Database for PostgreSQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.reviewer: maghan
ms.date: 09/13/2023
ms.service: architecture-center
ms.subservice: well-architected
ms.topic: conceptual
---

# Azure Well-Architected Framework review - Azure Database for PostgreSQL

This article provides architectural best practices for Azure Database for PostgreSQL.

The guidance is based on the five pillars of architectural excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency



## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend you review your workload using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

[Azure Database for PostgreSQL](/azure/PostgreSQL/overview) is a relational database service in Azure based on the PostgreSQL open-source relational database. It's a fully managed database as a service offering that can handle mission-critical workloads with predictable performance, security, high availability, and dynamic scalability. Azure Database for PostgreSQL is built on the community edition of the PostgreSQL database engine. It's compatible with the PostgreSQL server community edition and supports PostgreSQL extension features such as PostGIS and TimescaleDB.

> [!NOTE]  
> To explore a light-weight solution idea that uses Azure Database for PostgreSQL to store analytical results from the Cognitive Services API, see [Intelligent apps using Azure Database for PostgreSQL](/azure/architecture/solution-ideas/articles/intelligent-apps-using-azure-database-for-postgresql).
> 
> For implementation guidance, see product documentation: [Build scalable apps in Azure Cosmos DB for PostgreSQL](/azure/cosmos-db/PostgreSQL/quickstart-build-scalable-apps-overview).

## Reliability

Azure Database for PostgreSQL - Flexible Server offers [high availability](/azure/reliability/reliability-postgresql-flexible-server) support by provisioning physically separate primary and standby replicas either within the same availability zone (zonal) or across availability zones (zone-redundant). This high availability model ensures that committed data is never lost if a failure happens. The model is also designed so the database doesn't become a single point of failure in your software architecture. Azure Database for PostgreSQL - Flexible Server provides features that protect data and mitigate downtime for your mission-critical databases during planned and unplanned downtime events. Built on top of the Azure infrastructure that offers robust resiliency and availability, the flexible server has business continuity features that provide fault protection, address recovery time requirements, and reduce data loss exposure.

### Reliability design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - Defined targets for RPO (Recovery Point Objective) and RTO (Recovery Time Objective) for workloads.
> - Select the appropriate high-availability configuration.
> - Configure geo-redundancy backup.
> - Test your disaster recovery plan to ensure rapid data restoration in case of a failure.
> - Test On-Demand Failover for your HA-enabled server to ensure our application behaves as expected.
> - Monitor your server to ensure it's healthy and performing as expected.

### Reliability recommendations

| Recommendation | Benefit |
| --- | --- |
| Defined targets for RPO (Recovery Point Objective) and RTO (Recovery Time Objective) for workloads. | Derive these values by conducting a risk assessment and ensuring you understand the cost and risk of downtime and data loss. These are nonfunctional requirements of a system and should be dictated by business requirements. |
| Select the appropriate high availability configuration. | Azure Database for PostgreSQL Server offers high availability configurations, ensuring that the service remains available if there's a zone outage and no data is lost. When high availability is configured, the Azure Database for PostgreSQL server automatically provisions and manages a standby replica. |
| Configure geo-redundancy backup. | Cross-region read replicas can be deployed to protect your databases from region-level failures. Geo Redundant backups are enabled in selected regions and help with disaster recovery in the event the primary server region is down. |
| Test your disaster recovery plan to ensure rapid data restoration if there's a failure. | Read replicas can be deployed on a different region and promoted to a read-write server if disaster recovery is needed. |
| Monitor your server to ensure it's healthy and performing as expected. | We have database monitoring in place to monitor and alert on database-level failures. |

## Security

Think about [security](/azure/well-architected/security/) throughout the entire lifecycle of an application, from design and implementation to deployment and operations. The Azure platform protects against various threats like network intrusion and DDoS attacks. You still need to build security into your application and your DevOps processes.

### Security design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - SSL and enforce encryption to secure data in transit.
> - Implement network security groups and firewalls to control access to your database.
> - Use Microsoft Entra ID for authentication and authorization to enhance identity management.
> - Configure row-level security.

### Security recommendations

| Recommendation | Benefit |
| --- | --- |
| SSL and enforce encryption to secure data in transit. | Deploy the DigiCert Global Root certificate from a trusted Certificate Authority (CA) certificate needed to communicate over SSL with client applications. |
| Implement network security groups and firewalls to control access to your database. | As part of the Zero Trust Model for security, network segmentation is recommended where communication paths between components (in this case, application and database server) are restricted to only what's needed. This can be implemented using Network Security Group and Application Security Groups. |
| Use Microsoft Entra ID for authentication and authorization to enhance identity management. | Microsoft Entra authentication is a mechanism of connecting to Azure Database for PostgreSQL using identities defined in Microsoft Entra ID. |
| Configure row-level security. | [Row level security (RLS)](https://www.postgresql.org/docs/current/ddl-rowsecurity.html) is a PostgreSQL security feature that allows database administrators to define policies to control how specific rows of data display and operate for one or more roles. Row-level security is an additional filter you can apply to a PostgreSQL database table. |

## Cost optimization

Cost optimization is about understanding your configuration options and recommended best practices to reduce unnecessary expenses and improve operational efficiencies. You should review your workload to identify opportunities to reduce costs.

### Cost design checklist

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

### Cost recommendations

| Recommendations | Benefits |
| --- | --- |
| Pick the right tier and SKU. | Pick the pricing tier and compute SKUs that support the specific needs of your workload. Azure Advisor gives you recommendations to optimize and reduce your overall Azure spending. Recommendations include server right-sizing that you should follow. |
| Understand high availability mode. | High availability makes a standby server always available within the same zone or region. Enabling high availability doubles your cost. |
| Adjust compute and storage tier.s | You should manually adjust the compute and storage tiers to meet the application's requirements over time.
| Use Start/Stop feature. | The Flexible server has a Start/Stop feature that you can use to stop the server from running when you don't need it.
| Consider reserved instances. | Consider a one or three-year reservation to receive significant discounts on computing. Use these reservations for workloads with consistent compute usage for a year or more. |
| Use your provisioned storage. | There's no extra charge for backup storage up to 100% of your total provisioned server storage.
| Understand redundancy costs. | Geo-redundant storage (GRS) costs twice as much as local redundant storage (LRS). GRS requires double the storage capacity of LRS.
| Evaluate storage scale-up decisions. | You should evaluate your current and future storage needs before scaling up your storage. After you scale up storage, you can't scale down.
| Deploy to the same region as the app. | Deploy to the same region as the application(s) to minimize transfer costs. When you use virtual network integration, applications in a different virtual network don't have direct access to flexible servers. To grant them access, you need to configure virtual network peering. Virtual network peering has nominal inbound and outbound data transfer costs. |
| High availability oriented cost description. | It's a trade-off of HA and costs. HA is double the cost for non-HA configuration, but it's needed. |
| Consolidate databases and servers. | You can consolidate multiple databases and servers into a single server to reduce costs. |

### Azure policy definitions

Azure Policy definitions help you enforce specific rules and configurations for resources within your Azure environment. To ensure cost optimization for Azure Database for PostgreSQL, you can create custom Azure Policy definitions to enforce specific configurations and best practices. Here's an example of some custom Azure Policy definitions you can create for cost optimization:

- [Optimize costs](/azure/postgresql/flexible-server/how-to-cost-optimization)

## Operational excellence

The principles of operational excellence are a series of considerations that can help achieve superior operational practices.

To achieve a higher competency in operations, consider and improve how software is developed, deployed, operated, and maintained.

### Operational excellence design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - Set up automated backups and retention policies to maintain data availability and meet compliance requirements.
> - Implement automated patching and updates to keep your PostgreSQL instance secure and up-to-date.
> - Monitor database health and performance using Azure Monitor and set up alerts for critical metrics.

### Operational excellence recommendations

| Recommendation | Benefits |
| --- | --- |
| Set up automated backups and retention policies to maintain data availability and meet compliance requirements. | Azure Database for PostgreSQL provides automated backups and point-in-time restore for your database. You can configure the retention period for backups up to 35 days. |
| Implement automated patching and updates to keep your PostgreSQL instance secure and up-to-date. | Azure Database for PostgreSQL provides automated patching and updates for your database. You can configure the maintenance window for your server to minimize the impact on your workload. |
| Monitor database health and performance using Azure Monitor and set up alerts for critical metrics. | Azure Database for PostgreSQL provides built-in monitoring and alerting capabilities. You can monitor the health and performance of your database using Azure Monitor. You can also set up alerts for critical metrics to get notified when your database isn't performing as expected. |

### Operational excellence policy definitions

Azure Policy definitions help you enforce specific rules and configurations for resources within your Azure environment. To ensure Operational excellence for Azure Database for PostgreSQL, you can create custom Azure Policy definitions to enforce specific configurations and best practices. Here's an example of some custom Azure Policy definitions you can create for Operational excellence:

- [Azure Policy Regulatory Compliance controls for Azure Database for PostgreSQL](/azure/postgresql/single-server/security-controls-policy)

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users efficiently. We recommend you review the Performance efficiency principles.

In the design checklist and list of recommendations below, call-outs indicate whether each choice applies to cluster architecture, workload architecture, or both.

### Performance efficiency design checklist

You should review the [design principles](/azure/architecture/framework/cost/principles) to optimize the cost of your architecture.

> [!div class="checklist"]
>  
> - Design your schema and queries for efficiency to minimize resource consumption.
> - Implement read replicas to offload read traffic and enhance overall performance.

### Performance efficiency recommendations

| Recommendation | Benefits |
| --- | --- |
| Design your schema and queries for efficiency to minimize resource consumption. | You should design your schema and queries for efficiency to minimize resource consumption. |
| Implement read replicas to offload read traffic and enhance overall performance. | You can use read replicas to offload read traffic and enhance performance. |

### Performance efficiency policy definitions

Azure Policy definitions help you enforce specific rules and configurations for resources within your Azure environment. To ensure Performance efficiency for Azure Database for PostgreSQL, you can create custom Azure Policy definitions to enforce specific configurations and best practices. Here's an example of some custom Azure Policy definitions you can create for Performance efficiency:

## Extra resources

Consider more resources related to Azure Database for PostgreSQL.

### Azure Architecture Center Guidance

- [Multitenancy and Azure Database for PostgreSQL](/azure/architecture/guide/multitenant/service/postgresql)
- [Best practices](https://azure.microsoft.com/blog/performance-best-practices-for-using-azure-database-for-postgresql/)
- [Optimize performance](https://azure.microsoft.com/blog/optimize-performance-using-azure-database-for-postgresql-recommendations/)
- [Tuning](https://azure.microsoft.com/blog/performance-updates-and-tuning-best-practices-for-using-azure-database-for-postgresql/)

### Cloud Adoption Framework guidance

- [Batch Data application with Azure Database for PostgreSQL](/azure/cloud-adoption-framework/scenarios/cloud-scale-analytics/architectures/data-landing-zone-data-products#batch-data-application)
