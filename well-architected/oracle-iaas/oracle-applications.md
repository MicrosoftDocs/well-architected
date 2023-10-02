---
title: Oracle on Azure IaaS design area compute and storage
description: Review the design area for storage and compute of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: 
ms.date: 
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-Oracle
---

## Application design

Oracle applications are complex. Especially the understanding of supported and unsupported functionalities from version to version introduces difficulties in the migration approach. And if an understanding of each application version is missing most migration fail.

Moreover companies don't just want to lift and shift, but modernize the architecture and bind to functional and non-functional requirements. These requirements should be examined alongside key cloud application design patterns to ensure aspirations are fully achieved.

> [!IMPORTANT]
> All Oracle applications are legacy systems and possess strong dependencies between the application tier and the database tier. Separating two tiers across different cloud vendors introduces latencies. Therefore you should do a proper technical assessment and decide if you are fine with it.

## Oracle application designs

Oracle workloads such as Siebel, E-Business Suite, JD Edwards, and Peoplesoft have specific application design patterns for Azure. Below are example Azure architecture diagrams and more product detail of each Oracle workload designed for setting up internal and external user access. Note the application design considerations and the Azure services used for each design.

## Siebel on Azure
Siebel is an on-premises CRM application that is used in many ways by companies. Siebel is one of the most complex applications in Oracle’s portfolio.

If you want to migrate your Siebel application, this must be done in the Siebel tool subnet. Make sure that the database version matches up with the application version. Otherwise, this could be a first reason a migration fails.  

In version 17 or newer, you need to configure certain servers and utilities on the application and database tier. As soon as the application tier is set up, all servers must be configured on the database as well. If all the tasks above haven't been conducted, either the migration will fail and/or the performance affected heavily.

The following design example provides a first idea of a generic Siebel Architecture on Azure for IP 16 and earlier:  

:::image type="content" source="./images/siebelip16-earlier-diagram.png" alt-text="Diagram of Siebel IP 16 or earlier architecture on Azure." lightbox="./images/siebelip16-earlier-diagram.png" border="false":::

The following design example provides a first idea of a generic Siebel Architecture on Azure for IP 17 and earlier:
:::image type="content" source="./images/siebelip17-earlier-diagram.png" alt-text="Diagram of Siebel IP 17 or later architecture on Azure." lightbox="./images/siebelip17-earlier-diagram.png" border="false":::

Refer to the Siebel design considerations:

**(1) Network & Security** - 

You should consider establishing an Azure AD for Single-Sign-On (SSO). The SSO method used is Security Assertions Markup Language (SAML). 
Microsoft allocates an [Azure AD Application Proxy](/azure/active-directory/app-proxy/what-is-application-proxy). You should consider it especially for remote users. Internal users should be routed through [ExpressRoute](/azure/expressroute/). If you desire a Firewall in front, [Azure Firewall](/azure/firewall/overview) can be configured as well.

In cases where external users need to access your application, the [Application Gateway](/azure/application-gateway/overview) provides a Web Application Firewall, but also a Layer 7 Load Balancing. Note that Application Gateways can only be used for http/s protocols. The subnets can be secured by using [Network Security Groups (NSG)](/azure/virtual-network/network-security-group-how-it-works). However, if you desire to grant access only to certain individuals you can also use [Role-Based-Access-Control (RBAC).](/azure/role-based-access-control/overview).

Because an SSH port is required for Siebel, a Bastion host as a jump box can also provide another security layer for an in-depth mature security posture.

**(2) Application Web Tier** - The web tier load balances the requests and sends the requests accordingly to the application tier, database tier and/or backup.
You can leverage any VM shape that fits into your usage. 


**(3) Application Tier** - The application tier contains several tasks and servers. The web tier directs the requests into the application tier, shared storage system, and the database. The application tier has strong dependencies between the application server and the shared file system. The shared file System has a direct connection to the database. Cutting the dependencies causes latencies. A proper technical architecture assessment should take place before considering cutting these dependencies to different cloud vendors.

When you set up a holistic architecture and technical approach for migration you have the ability to establish it more modern. Microsoft offers various options. Auto-Scaling is interesting for companies that have events where they need to scale on high demands.

Virtual Machine Scale Sets can provide another value for Siebel. The Siebel Workflow Servers contain much information and for some customers it scales high and no need to redo architecture. Virtual Machine Scale Sets lets you create and manage a group of load-balanced VMs and provides high availability.
In case you only require high availability, please review [AVailability Sets](https://learn.microsoft.com/en-us/azure/virtual-machines/availability-set-overview)

**(4) Database Tier** - The Database tier has one primary and replicated to a secondary using Data Guard. 
-	In one region you can make use of a synchronous configuration
-	Across regions use asynchronous configuration
    - If you prefer a zero data-loss strategy we suggest you using the asynchronous configuration
-	Data Guard is only one of the options you can leverage. Other examples could be: Striim, Attunity, Goldengate or Active Data Guard 

**(5) Backup** - [Backups](/azure/backup/backup-azure-vms-introduction) are sent from the application tier and the database tier. It's just one of many reasons why those two tiers shouldn't be separated into two different vendors. Thereby Backups of the database are performed by [Azure Backup Volume Snapshot](https://techcommunity.microsoft.com/t5/data-architecture-blog/azure-backup-volume-snapshots-for-oracle-is-now-ga/ba-p/2820032) on Premium Files to the secondary region.

**(6) Disaster Recovery** - There are different solutions you can choose from. It very much depends on your requirements. The architecture above is built to be highly available. For replicating the application tier you can use [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Another solution you can choose is [Redundancy options for managed disks.](/azure/virtual-machines/disks-redundancy#zone-redundant-storage-for-managed-disks) Both solutions replicate your data. Redundancy options for managed disks are a solution that can simplify the architecture but also comes with a few limitations.

## E-Business Suite on Azure
Oracle E-Business Suite (EBS) is a suite of applications including Supply Chain Management (SCM) and Customer Relationship Management (CRM). As EBS is a SCM and CRM system, it usually has many interfaces to third-party systems. The below architecture is built to be highly available within one region.  

We assume that external users don't cross the corporate network in the diagram below:

:::image type="content" source="./images/ebs-diagram.png" alt-text="Diagram of EBS architecture on Azure." lightbox="./images/ebs-diagram.png" border="false":::

Refer to the Oracle EBS design considerations:

**(1) Network & Security** - You should consider establishing an Azure AD for Single-Sign-On (SSO). The SSO method used is Security Assertions Markup Language (SAML). Microsoft allocates an [Azure AD Application Proxy](/azure/active-directory/app-proxy/what-is-application-proxy). You should consider it especially for remote users. Internal users should be routed through [ExpressRoute](/azure/expressroute/). If you desire a Firewall in front, [Azure Firewall](/azure/firewall/overview) can be configured as well.

In cases where external users need to access your application the [Application Gateway](/azure/application-gateway/overview) provides a Web Application Firewall, but also a Layer 7 Load Balancing. Note that Application Gateways can only be used for http/s protocols. The subnets can be secured by using [Network Security Groups (NSG)](/azure/virtual-network/network-security-group-how-it-works). However if you desire to grant access only to certain individuals you can also use [Role-Based-Access-Control (RBAC).](/azure/role-based-access-control/overview)

Because an SSH port is required for EBS, a Bastion host as a jump box can also provide another security for an in-depth mature security posture.

**(2) Application Tier** - The application tier consists of more than two application servers. Usually, the rules for the application server are saved on application web servers. Evaluate the access you need to establish. If users only access the web tier but aren't allowed to access the application server, consider allocating the tiers into application web tier and application tier.

However, if the application tier and the database tier will be cut into two different cloud vendors, it can cause high latencies. We recommend you to do a proper technical assessment and decide if high latencies will impact your daily business.  

As soon as you approach the technical assessment, you should consider modernizing it. Some customers ask for auto-scaling methods. Auto-Scaling can be achieved through [Virtual Machine Scale Sets (Virtual Machine Scale Sets)](/azure/virtual-machine-scale-sets/overview) for EBS. Some of the servers that you have in use operate more tasks and create a higher throughput than others. This should be evaluated during the Design Phase. Virtual Machine Scale Sets don't require a rearchitecture and let you create and manage a group of load-balanced VMs that provides high availability.
In case you only require high availability, please review [AVailability Sets](https://learn.microsoft.com/en-us/azure/virtual-machines/availability-set-overview).

**(3) Database Tier** - The Database tier has one primary and replicated to a secondary using Data Guard. 
-	In one region you can make use of a synchronous configuration
-	Across regions use asynchronous configuration
    - If you prefer a zero data-loss strategy we suggest you using the asynchronous configuration
-	Data Guard is only one of the options you can leverage. Other examples could be: Striim, Attunity, Goldengate or Active Data Guard 

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

**(2) Application Web Tier** - The application web tier consists of more than two application servers. Usually, the rules for the application server are saved on application web servers. Within the presentation tier, each instance is associated with storage. Cutting the dependencies can cause high latencies. Therefore we recommend to do a proper technical assessment.

As an often requested auto-scaling method you can explore Virtual Machine Scale Sets for JDE.

Some of the servers that you have in use operate more tasks and create a higher throughput than others. This throughput should be evaluated during the Design Phase. Virtual Machine Scale Sets don't require a rearchitecture and let you create and manage a group of load-balanced VMs which also high availability.
In case you only require high availability, please review [AVailability Sets](https://learn.microsoft.com/en-us/azure/virtual-machines/availability-set-overview).

**(3) Database Tier** - The Database tier has one primary and replicated to a secondary using Data Guard. 
-	In one region you can make use of a synchronous configuration
-	Across regions use asynchronous configuration
    - If you prefer a zero data-loss strategy we suggest you using the asynchronous configuration
-	Data Guard is only one of the options you can leverage. Other examples could be: Striim, Attunity, Goldengate or Active Data Guard 

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
In case you only require high availability, please review [AVailability Sets](https://learn.microsoft.com/en-us/azure/virtual-machines/availability-set-overview).

**(4) Database Tier** - The Database tier has one primary and replicated to a secondary using Data Guard. 
-	In one region you can make use of a synchronous configuration
-	Across regions use asynchronous configuration
    - If you prefer a zero data-loss strategy we suggest you using the asynchronous configuration
-	Data Guard is only one of the options you can leverage. Other examples could be: Striim, Attunity, Goldengate or Active Data Guard 

**(5) Backup** - [Backups](/azure/backup/backup-azure-vms-introduction) are sent from the application tier and the database tier. Thereby Backups of the database are performed by [Azure Backup Volume Snapshot](https://techcommunity.microsoft.com/t5/data-architecture-blog/azure-backup-volume-snapshots-for-oracle-is-now-ga/ba-p/2820032) on Premium Files to the secondary region.

**(6) Disaster Recovery** - There are different solutions you can choose from. It very much depends on your requirements. The earlier mentioned architecture is built to be highly available. For replicating the application tier you can use [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Another solution you can choose is [Redundancy options for managed disks.](/azure/virtual-machines/disks-redundancy#zone-redundant-storage-for-managed-disks) Both solutions replicate your data. Redundancy options for managed disks are a solution that can simplify the architecture but also comes with a few limitations.


## Recommendations for Oracle on Azure:

| Recommendation | Benefit |
| --- | --- |
| Define functional and non-functional requirements  | Includes the access of individuals, your backup strategy, RPO&RTO, availability, storage strategy to cost optimization, data retention, and security requirements  |
| Right-Size your environment | Provide AWRs for your databases on peak-load and an overview about the statistics of the application server |
| Provide the version of your application | Different versions of Oracle Application have different limitations. You can help us to assess the right migration approach by just providing your application version |
| Security | Assess the version of your application for SSO methods. Some versions come with certain limitations |
| Availability | Microsoft offer a few availability methods of which you can make use of: Availability Sets, Proximity Placement Groups, HA Storage types |

Oracle and Microsoft have partnered to create a number of ready-to-deploy solutions for Oracle WebLogic Server on Azure IaaS. To get started, see [What are solutions for running Oracle WebLogic Server on Azure Virtual Machines?](/azure/virtual-machines/workloads/oracle/oracle-weblogic).


## Next Steps

In case you will need to right-size your Oracle application, we suggest to review Design Area [Compute and Storage][\compute-and-storage.md].