---
title: Oracle workloads
description: Oracle workloads best practices
author: ckittel
ms.author: kegorman
ms.date: 12/29/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Oracle workload best practices

The section strives to address Oracle workloads comprising not only Oracle Databases, but also applications such as Siebel, Peoplesoft, JD Edwards, E-Business Suite or customized Weblogic applications.

Customers around the globe are looking for hosting Oracle Workloads on Azure. For that reason, Microsoft provides solutions for each workload and coherent requirements. The most requirements customers asked for are around network and reducing latencies. Next to these auto-scaling solutions are asked for enormously.  

The section about Oracle Workloads on Azure provides guidance that applies Well-Architected best practices as the technical foundation for building and operating a highly reliable solution on Azure at-scale.

## About Oracle workloads

The term workload refers to a collection of database and application resources that support a common business goal or the execution of a common business process. These workloads could be Customer Relationship Management applications, Human Resources, customized application that mostly relies on Weblogic and others. Weblogic, Siebel, Peoplesoft, JD Edwards and E-Business Suite are Oracle on-premise applications.  

Oracle Applications on Azure and its approach are described in Section Application Design.

An Oracle Workload therefore describes a collection of application resources and databases, which must be highly available on the platform. The workload must always be resilient to failures.

## What are the common challenges

Microsoft Azure makes it easy to deploy and manage cloud solutions. However, building and migrating Oracle Workloads that are highly available and reliable on the platform remains a challenge for these main reasons:

- **Oracle applications architectures are complex**. The complexity relies on the dependencies between the application and database tier, Version upgrades and patches. Siebel has an architecture change between the version IP16 and IP17. The architecture change results in adjustments on the application and database tier to configure application servers on the database layer.
- **Designing a reliable application at scale requires knowledge about the application versions and architectures as well as dependencies on each layer**. By providing in-depth knowledge, we can help you to select the right technologies and guide you through the configuration setup.
- **Oracle database architectures differ from customer to customer**. Oracle RAC, Exadata features like SmartScan put complexity into the setup. Automatic Workload Repositories provide a great insight into the actual usage of Exadata Features or RAC Setup’s. Microsoft Azure provides solutions for every challenge so that we can address most of your needs.  

All Oracle Workloads need to be architected to handle failures with correlated or cascading impact. Reliability Engineering is an important task within the entire architecture design.

## Are Oracle Workloads Only About Reliability?

While the primary focus in on Reliability and creating highly available applications and databases other pillars of the Well-Architected Framework are equally important during the architecture design and migration phase.

- **Security**: The way user access applications and the holistic development of a workload architecture to mitigate security threats, such as Distributed Denial of Service (DDoS) attacks, will have a significant bearing on overall reliability.
- **Operational Excellence**: Potentially reached throughput or Memory on the database respond to operational issues will have a direct impact on application availability. Latencies should be eliminated to a minimum.
- **Performance Efficiency**: Availability is more than simple uptime, but rather a consistent level of application service and performance relative to a known healthy state.

Achieving high reliability imposes significant cost tradeoffs, which may not be justifiable for every workload scenario. It's therefore recommended that design decisions be driven by business requirements.

## What are the key design areas?

Oracle on Azure guidance within this series is composed of architectural considerations and recommendations orientated around these [key design areas](/azure/architecture/framework/mission-critical/mission-critical-overview).

:::image type="content" source="./images/mission-critical-design-areas.svg" alt-text="Diagram that shows mission critical design areas." lightbox="./images/mission-critical-design-areas.svg" border="false":::

| Design area | Summary |
| --- | --- |
| Application design |The use of a scale-unit architecture in the context of lift and shift highly reliable Oracle Applications. Also explores the cloud application design patterns that allow for scaling, and error handling. Next to this explore application dependencies. |
| Data Platform | Oracle on Azure offer different Virtual Machine sizes. The choice is based on the statistics of an AWR report delivered by the customer. AWR should always be handed in on peak-load.|
| Security | Protect the application and the database against threats intended to compromise its reliability directly or indirectly.|
| Networking & Connectivity | Network topology concepts at an application level. Includes required ports, latency. Critical recommendations intended to inform the design of a secure and scalable global network topology.|
| Operational Procedures | Adoption of Infrastructure as Code and related deployment methods is used to drive effective and consistent operational procedures.|
| Health Modeling | Processes to define a robust health model, mapping quantified application health states through observability and operational constructs to achieve operational maturity.|
| Deployment & Testing | Eradicate downtime and maintain application health for deployment operations, providing key considerations and recommendations intended to inform the design of optimal upgrades of application and database.|

We invite you to explore Oracle workload design best practices and return to this content regularly throughout the lifecycle of your Oracle workload. The content highlights critical areas of focus but also refers you to other documentation for deeper technical insight.

## Application Design

Oracle Applications are complex. Especially the understanding of supported and unsupported functionalities from version to version make a lift and shift painful. And if an understanding of each Application version is missing most migration fails.  

Moreover companies don't just want to lift and shift, but modernize the architecture and bind to functional and non-functional requirements. However, these requirements should be examined alongside key cloud application design patterns to ensure aspirations are fully achieved.

The design areas below explores both the subscription scale-unit approach and important application design patterns for building highly reliable Siebel, E-business Suite, and JD Edwards applications on Azure.

> [!IMPORTANT]
> All Oracle applications are legacy systems and possess strong dependencies between the application tier and the database tier. Separating two tiers across different cloud vendors introduces latencies. Therefore you should do a proper technical assessment and decide if you are fine with it.

### Subscription Scale-Unit approach

Some large enterprises require the same architecture across regions without installing and migrating everything from scratch. Customers also have commercial requirements by having everything in just one subscription.

This image demonstrates how the single subscription reference deployment model can be expanded across multiple subscriptions, in an extreme scale scenario, to navigate subscription scale-limits.

:::image type="content" source="./images/mission-critical-subscription-scale.gif" alt-text="Diagram that shows how subscription scale approach." lightbox="./images/mission-critical-subscription-scale.gif" border="false":::

### Siebel on Azure
Siebel is an on-premises CRM application. Companies use it differently. Siebel is the most complex application in Oracle’s portfolio because of an architecture change from version 16 to 17.  

If you want to migrate your Siebel application, this must be done in the Siebel Tool subnet. Make sure that the database version matches up with the application version. Otherwise, this could be a first reason a migration fails.  

As soon as you reach version 17, you need to configure certain servers and utilities on the application and database tier. As soon as the application tier is set up, all servers must be configured on the database as well. If all the tasks above haven't been conducted, either the migration will fail and/ or the performance affected heavily.  

The following design example provides a first idea of a generic Siebel Architecture on Azure for IP 16 and earlier:  

:::image type="content" source="./images/siebelip16-earlier-diagram.png" alt-text="Diagram of Siebel IP 16 or earlier architecture on Azure." lightbox="./images/siebelip16-earlier-diagram.png" border="false":::

The following design example provides a first idea of a generic Siebel Architecture on Azure for IP 17 and earlier:
:::image type="content" source="./images/siebelip17-earlier-diagram.png" alt-text="Diagram of Siebel IP 17 or later architecture on Azure." lightbox="./images/siebelip17-earlier-diagram.png" border="false":::

Refer to the Siebel design considerations:

**(1) Network & Security** - You should consider establishing an Azure AD for Single-Sign-On (SSO). The SSO method used is Security Assertions Markup Language (SAML). Microsoft allocates an [Azure AD Application Proxy](/azure/active-directory/app-proxy/what-is-application-proxy). You should consider it especially for remote users. Internal users should be routed through [ExpressRoute](/azure/expressroute/). If you desire a Firewall in front, [Azure Firewall](/azure/firewall/overview) can be configured as well.

In cases where external users need to access your application, the [Application Gateway](/azure/application-gateway/overview) provides a Web Application Firewall, but also a Layer 7 Load Balancing. Note that Application Gateways can only be used for http/s protocols. The subnets can be secured by using [Network Security Groups (NSG)](/azure/virtual-network/network-security-group-how-it-works). However if you desire to grant access only to certain individuals you can also use [Role-Based-Access-Control (RBAC).](/azure/role-based-access-control/overview)

Because an SSH port is required for Siebel, a Bastion host as a jump box can also provide another security for an in-depth mature security posture.

**(2) Application Web Tier** - The web tier load balances the requests and send the requests accordingly to the application tier, database tier and/or backup.

**(3) Application Tier** - The application tier contains several tasks and servers. The web tier directs the requests into the application tier, shared storage system, and the database. The application tier has strong dependencies between the application server and the shared file system. The shared file System has a direct connection to the database. Cutting the dependencies causes latencies. A proper technical architecture assessment should take place before considering cutting these dependencies to different cloud vendors.

When you set up a holistic architecture and technical approach for migration you have the ability to establish it more modern. Microsoft offers various options. Auto-Scaling is interesting for companies that have events where they need to scale on high demands.

Virtual Machine Scale Sets can provide another value for Siebel. The Siebel Workflow Servers contain much information and for some customers it scales high and no need to redo architecture. Virtual Machine Scale Sets lets you create and manage a group of load-balanced VMs and provides high availability.

**(4) Database Tier** - The database tier has one primary and replicated to a secondary using Data Guard. If you stay within one datacenter, the synchronous configuration should be used. If you install your application across datacenters, you should configure Data Guard in Asynchronous mode.

**(5) Backup** - [Backups](/azure/backup/backup-azure-vms-introduction) are sent from the application tier and the database tier. It's just one of many reasons why those two tiers shouldn't be separated into two different vendors. Thereby Backups of the database are performed by [Azure Backup Volume Snapshot](https://techcommunity.microsoft.com/t5/data-architecture-blog/azure-backup-volume-snapshots-for-oracle-is-now-ga/ba-p/2820032) on Premium Files to the secondary region.

**(6) Disaster Recovery** - There are different solutions you can choose from. It very much depends on your requirements. The architecture above is built to be highly available. For replicating the application tier you can use [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Another solution you can choose is [Redundancy options for managed disks.](/azure/virtual-machines/disks-redundancy#zone-redundant-storage-for-managed-disks) Both solutions replicate your data. Redundancy options for managed disks are a solution that can simplify the architecture but also comes with a few limitations.

### E-Business Suite on Azure
Oracle E-Business Suite (EBS) is a suite of applications including Supply Chain Management (SCM) and Customer Relationship Management (CRM). As EBS is a SCM and CRM system, it usually has many interfaces to third-party systems. The below architecture is built to be highly available within one region.  

We assume that external users don't cross the corporate network in the diagram below:

:::image type="content" source="./images/ebs-diagram.png" alt-text="Diagram of EBS architecture on Azure." lightbox="./images/ebs-diagram.png" border="false":::

Refer to the Oracle EBS design considerations:

**(1) Network & Security** - You should consider establishing an Azure AD for Single-Sign-On (SSO). The SSO method used is Security Assertions Markup Language (SAML). Microsoft allocates an [Azure AD Application Proxy](/azure/active-directory/app-proxy/what-is-application-proxy). You should consider it especially for remote users. Internal users should be routed through [ExpressRoute](/azure/expressroute/). If you desire a Firewall in front, [Azure Firewall](/azure/firewall/overview) can be configured as well.

In cases where external users need to access your application the [Application Gateway](/azure/application-gateway/overview) provides a Web Application Firewall, but also a Layer 7 Load Balancing. Note that Application Gateways can only be used for http/s protocols. The subnets can be secured by using [Network Security Groups (NSG)](/azure/virtual-network/network-security-group-how-it-works). However if you desire to grant access only to certain individuals you can also use [Role-Based-Access-Control (RBAC).](/azure/role-based-access-control/overview)

Because an SSH port is required for Siebel, a Bastion host as a jump box can also provide another security for an in-depth mature security posture.

**(2) Application Tier** - The application tier consists of more than two application servers. Usually, the rules for the application server are saved on application web servers. Evaluate the access you need to establish. If users only access the web tier but aren't allowed to access the application server, consider allocating the tiers into application web tier and application tier.

However, if the application tier and the database tier will be cut into two different cloud vendors, it can cause high latencies. We recommend you to do a proper technical assessment and decide if high latencies will impact your daily business.  

As soon as you approach the technical assessment, you should consider modernizing it. Some customers ask for auto-scaling methods. Auto-Scaling can be achieved through [Virtual Machine Scale Sets (Virtual Machine Scale Sets)](/azure/virtual-machine-scale-sets/overview) for EBS. Some of the servers that you have in use operate more tasks and create a higher throughput than others. This should be evaluated during the Design Phase. Virtual Machine Scale Sets don't require a rearchitecture and let you create and manage a group of load-balanced VMs that provides high availability.

**(3) Database Tier** - The database tier has one primary and replicated to a secondary using Data Guard. If you stay within one datacenter, the synchronous configuration should be used. If you install your application across datacenters, you should configure Data Guard in Asynchronous mode.

**(4) Backup** - [Backups](/azure/backup/backup-azure-vms-introduction) are sent from the application tier and the database tier. Thereby Backups of the database are performed by [Azure Backup Volume Snapshot](https://techcommunity.microsoft.com/t5/data-architecture-blog/azure-backup-volume-snapshots-for-oracle-is-now-ga/ba-p/2820032) on Premium Files to the secondary region.

**(5) Disaster Recovery** - There are different solutions you can choose from. It very much depends on your requirements. The architecture above is built to be highly available. For replicating the application tier you can use [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Another solution you can choose is [Redundancy options for managed disks.](/azure/virtual-machines/disks-redundancy#zone-redundant-storage-for-managed-disks) Both solutions replicate your data. Redundancy options for managed disks are a solution that can simplify the architecture but also comes with a few limitations.

### JD Edwards on Azure

Oracle's JD Edwards is an integrated applications suite of comprehensive enterprise resource planning software. We have seen JDE used in Supply chain, Warehouse Management, Logistics, Manufacturing resource planning and more. Because of the use of the application, we see that interfaces to other systems are important as well.

The following architecture is built to being highly available. We assumed that external users aren't accessing over the corporate network. If an external user accesses the application using corporate network, the architecture can be simplified on networking.

:::image type="content" source="./images/jde-architecture.png" alt-text="Diagram of JD Edwards architecture on Azure." lightbox="./images/jde-architecture.png" border="false":::

Refer to the JD Edwards design considerations:

**(1) Network & Security** - You should consider establishing an Azure AD for Single-Sign-On (SSO). The SSO method used is Security Assertions Markup Language (SAML). Microsoft allocates an [Azure AD Application Proxy](/azure/active-directory/app-proxy/what-is-application-proxy). You should consider it especially for remote users. JDE does only offer an ssh port. In that case we recommend you to set a Firewall in front by using [Azure Firewall](/azure/firewall/overview) to properly protect your application installation for external user.

Internal users can access your application through ExpressRoute. If desired, a Web Application Firewall on Azure Front Door can be configured as well.  

The subnets can be secured by using [Network Security Groups (NSG)](/azure/virtual-network/network-security-group-how-it-works). However if you desire to grant access only to certain individuals you can also use [Role-Based-Access-Control (RBAC).](/azure/role-based-access-control/overview)

Because an SSH port is required for JDE, a Bastion host as a jump box is recommended and can provide another security for an in-depth mature security posture.

**(2) Application Web Tier** - The application web tier consists of more than two application servers. Usually, the rules for the application server are saved on application web servers. Within the presentation tier, each instance is associated with storage. The middle tier has a direct connection to the database where requests are directed. Therefore we recommend to do a proper technical assessment.

As an often requested auto-scaling method you can explore Virtual Machine Scale Sets for JDE.

Some of the servers that you have in use operate more tasks and create a higher throughput than others. This throughput should be evaluated during the Design Phase. Virtual Machine Scale Sets don't require a rearchitecture and let you create and manage a group of load-balanced VMs which also high availability.

**(3) Database Tier** - The database tier has one primary and replicated to a secondary using Data Guard. If you stay within one region, the synchronous configuration should be used. If you install your application across regions, you should configure Data Guard in Asynchronous mode. Data from the database tier are sent directly to an Azure Storage. The Storage is dependent on your current architecture setup.

**(4) Backup** - [Backups](/azure/backup/backup-azure-vms-introduction) are sent from the application tier and the database tier. It's just one of many reasons why those two tiers shouldn't be separated into two different vendors. Thereby Backups of the database are performed by [Azure Backup Volume Snapshot](https://techcommunity.microsoft.com/t5/data-architecture-blog/azure-backup-volume-snapshots-for-oracle-is-now-ga/ba-p/2820032) on Premium Files to the secondary region.

**(5) Disaster Recovery** - There are different solutions you can choose from. It very much depends on your requirements. The architecture above is built to be highly available. For replicating the application tier you can use [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Another solution you can choose is [Redundancy options for managed disks.](/azure/virtual-machines/disks-redundancy#zone-redundant-storage-for-managed-disks) Both solutions replicate your data. Redundancy options for managed disks are a solution that can simplify the architecture but also comes with a few limitations.

### Peoplesoft on Azure

Oracle's PeopleSoft application suite contains software for human resources and financial management. The application suite is multi-tiered, and applications include human resource management systems (HRMS), customer relationship management (CRM), financials and supply chain management (FSCM), and enterprise performance management (EPM).

:::image type="content" source="./images/peoplesoft-architecture.png" alt-text="Diagram of Peoplesoft architecture on Azure." lightbox="./images/peoplesoft-architecture.png" border="false":::

Refer to the Peoplesoft design considerations:

**(1) Network & Security** - You should consider establishing an Azure AD for Single-Sign-On (SSO). The SSO method used is Security Assertions Markup Language (SAML). Microsoft allocates a [Azure AD Application Proxy](/azure/active-directory/app-proxy/what-is-application-proxy). You should consider it especially for remote users.

Internal users should be routed through [ExpressRoute](/azure/expressroute/). If you desire a firewall in front, you configure [Azure Firewall](/azure/firewall/overview).

In cases where external users need to access your application, the [Application Gateway](/azure/application-gateway/overview) provides a Web Application Firewall, but also a Layer 7 Load Balancing. Note that the Application Gateway can only be used for http/s protocols.

The subnets can be secured by using [Network Security Groups (NSG)](/azure/virtual-network/network-security-group-how-it-works). However if you desire to grant access only to certain individuals you can also use [Role-Based-Access-Control (RBAC)](/azure/role-based-access-control/overview).

Because an SSH port is required for Peoplesoft, a Bastion host as a jump box can provide extra security for an in-depth mature security posture.

> [!IMPORTANT]
> Check the your operating systems for proper architecture and try to migrate as much to a Linux OS as possible.

**(2) Application Web Tier** - Requests entering the web tier are processed and sent to the application tier, database tier or Backup. The Elastic Search includes the indexes for the Peoplesoft application.

**(3) Application Tier** - The application tier contains several tasks and servers. It runs the business logic and processes but also maintains the connection to the database. As soon as this dependency is cut, it causes latencies. Therefore, the application and the database tier shouldn't be separated into two different vendors. The Process Scheduler is only required for Windows operating systems. For that reason, the architecture should be evaluated carefully.

Microsoft offers a couple of features for establishing a modern architecture. Auto-Scaling is interesting for companies that have events where they need to scale on high demands.

**(4) Database Tier** - The Database tier has one primary and replicated to a secondary using Data Guard. If you stay within one datacenter, the synchronous configuration should be used. If you install your application across datacenters, you should configure Data Guard in Asynchronous mode.

**(5) Backup** - [Backups](/azure/backup/backup-azure-vms-introduction) are sent from the application tier and the database tier. Thereby Backups of the database are performed by [Azure Backup Volume Snapshot](https://techcommunity.microsoft.com/t5/data-architecture-blog/azure-backup-volume-snapshots-for-oracle-is-now-ga/ba-p/2820032) on Premium Files to the secondary region.

**(6) Disaster Recovery** - There are different solutions you can choose from. It very much depends on your requirements. The earlier mentioned architecture is built to be highly available. For replicating the application tier you can use [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Another solution you can choose is [Redundancy options for managed disks.](/azure/virtual-machines/disks-redundancy#zone-redundant-storage-for-managed-disks) Both solutions replicate your data. Redundancy options for managed disks are a solution that can simplify the architecture but also comes with a few limitations.

### Choose the right VM for your Workload

Every database and server is used to different capacities. Therefore, it is important to extract AWR reports on peak-load.

On the application tier, prepare numbers and statistics from the Web tier and the application tier. Statistics should include:

- Name of the Server
- Number of CPUs
- Average utilization of CPU  
- Memory Size
- Average utilization  
- App/ DB Storage Size
- App and DB version
- Operating system
- Total IOPS
- Total Throughput
- Backup strategy

The [E](/azure/virtual-machines/edv5-edsv5-series) and [M](/azure/virtual-machines/mv2-series)-series Virtual Machines are the right to choose from. Each Virtual Machine comes with a different size and can be perfectly matched to your needs. Microsoft also offers [constrained VMs](/azure/virtual-machines/constrained-vcpu). These Virtual Machine are designed for customers not in need of many CPU but memory.

Recommendations for Oracle on Azure:

| Recommendation | Benefit |
| --- | --- |
| Define functional and non-functional requirements  | Includes the access of individuals, your backup strategy, RPO&RTO, availability, storage strategy to cost optimization, data retention, and security requirements  |
| Right-Size your environment | Provide AWRs for your databases on peak-load and an overview about the statistics of the application server |
| Provide the version of your application | Different versions of Oracle Application have different limitations. You can help us to assess the right migration approach by just providing your application version |
| Security | Assess the version of your application for SSO methods. Some versions come with certain limitations |
| Availability | Microsoft offer a few availability methods of which you can make use of: Proximity Placement Groups, HA Storage types |

## Illustrative Examples

The Oracle workload guidance provided within this series is based on a solution-orientated approach to illustrate key design considerations and recommendations. There are several reference implementations and scripts available.

For more information, see the [Oracle reference architecture](/azure/virtual-machines/workloads/oracle/oracle-reference-architecture).

We invite you to explore Oracle workload design best practices and return to this content regularly throughout the lifecycle of your Oracle workload. The content highlights critical areas of focus but also refers you to other documentation for deeper technical insight.

For more information, see:

- [Azure Center for Oracle Solutions]( /azure/center-Oracle-solutions/overview)
- [Oracle workload in Azure](/azure/virtual-machines/workloads/Oracle/get-started)
- [Oracle workload architectures](/azure/architecture/reference-architectures/Oracle/Oracle-overview)

## Next Steps

>[!div class="nextstepaction"]
>[Reliability](./reliability.md)

>[!div class="nextstepaction"]
>[Security](./security.md)

>[!div class="nextstepaction"]
>[Cost Optimization](./cost-optimization.md)

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)