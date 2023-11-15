---
title: Oracle on Azure IaaS design area compute and storage
description: Review the design area for BCDR of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: 
ms.date: 
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-Oracle
---

# Business Continuity and Disaster Recovery (BCDR)

When discussing reliability with Oracle in Azure, it’s important to take into consideration not just the database but also the connected tiers on separate VMs, virtual network subnets and connected storage components. Oracle on Azure IaaS can achieve the required resiliency objectives of the most demanding Oracle workloads. The guidelines offered here are most effective when resiliency KPIs are defined based on business requirements. RPO and RTO requirements will provide you a baseline KPI to decide on your best suited architecture. 
- Recovery Time Objective (RTO): The maximum time an application is unavailable after an incident.
- Recovery Point Objective (RPO): The maximum amount of data loss exposed during a disaster.

## Data Protection (Backups)

There are two possible types of Oracle database backup that are relevant when the Oracle workload is implemented on Azure IaaS:
- Streaming backups using Oracle RMAN (Recovery Manager) which is originally based on backups being streamed to sequential tape media. This type of backup typically has two possible destinations on Azure: Virtual Tape libraries from third party providers on the Azure marketplace, or local and remote Fileshares such as Azure Blob NFS, Azure Files and Azure NetApp Files.
- Storage-level snapshots typically using Azure backup service. This backup method depends on the type of storage used for database files. For example, for Azure Managed disk (premium SSD) [Azure Backup is integrated with the Oracle database](https://learn.microsoft.com/en-us/azure/virtual-machines/workloads/oracle/oracle-database-backup-azure-backup?tabs=azure-portal) while for Azure NetApp Files there are data protection capabilities such as [ANF backup](https://learn.microsoft.com/en-us/azure/azure-netapp-files/backup-introduction) and [cross-region replication](https://learn.microsoft.com/en-us/azure/azure-netapp-files/cross-region-replication-introduction).

It is important to note that with streaming backups of large databases, the time taken to copy the data back in order to restore it can exceed the RTO requirements. In that case the storage-level snapshots become the better option.

### Assessment question
Have you implemented and tested (or do you have plans to implement and test) backup strategies for your Oracle on Azure IaaS deployment?

### Recommendations
- Consider carefully whether to implement a backup strategy based on Streaming (RMAN), storage-level snapshots or even both. 
- Assess the effect of your backup strategy on the RTO and RPO requirements.
- Analyze the available storage destinations for your RMAN backups based on the documented throughput limits for the different options in order to choose the option that meets your requirements.
- Consider using the Azure Backup service for your storage-level snapshots and consider placing the snapshots in a paired region or an availability zone for extra protection.
- Consider the different storage options available to store the Archive log backups needed to recover the database from performance, replication and cost points of view. 
- Develop and regularly test your backup/restore plans to avoid having any unwanted surprises in your production environment.

## Service Protection (High Availability) and Business Continuity (Disaster Recovery)

Service protection and business continuity considerations aim to produce architectural redundancy for the purpose of maximizing the time the service is available. This means minimizing any service downtime due to planned outages (e.g. patching, updates, upgrades etc.) and unplanned outages (e.g. failures), and recovering from geography-wide failures.

Azure provides many options for the high availability of the individual components deployed in an Oracle on IaaS Architecture. Virtual machines can be deployed in availability sets to guarantee separate fault domains and update domains, availability zones are used to protect against data center failures, and deployment in different regions can protect against the failure of a full region. The different Azure storage capabilities have a variety of storage redundancy levels such as LRS, ZRS and GRS. All these options should be considered when putting these components together to deploy the Oracle workload on IaaS.

In addition, Oracle provides [Dataguard](https://learn.microsoft.com/en-us/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard), which is the most common tool used for Oracle database service protection setups. While usually used for database recovery, transaction logs can be forwarded to a standby database and applied to maintain another database which is an exact copy to failover to. Dataguard has three modes of data replication (Maximum Protection, Maximum Availability and Maximum Performance) which offer different combinations of log transport modes and guarantees for transactions application on the secondary database.

Oracle [Goldengate](https://learn.microsoft.com/en-us/azure/virtual-machines/workloads/oracle/configure-oracle-golden-gate) is another tool that can replicate data between two databases and can enable multi master scenarios. Goldengate is an extra-cost option as compared with Dataguard which is integrated into Oracle Enterprise Edition.

### Assessment question
Have you implemented and tested (or do you have plans to implement and test) HA and DR strategies for your Oracle on Azure IaaS deployment?

### Recommendations
- Take into consideration the capabilities that Azure provides for the High Availability of the different Infrastructure components in your Oracle on IaaS implementation.
- When using Dataguard for HA and DR, carefully select the data replication mode that fulfills your requirements. For example using Maximum Performance mode provides the minimum impact on the source but the highest potential for data loss.
- Consider automating your failover process e.g. using Fast-start Failover.
- Establish test procedures for your failover processes and carry out regular testing to avoid any issues.
- Architect your solution holistically by utilizing the Azure native capabilities (e.g. Availability Zones) and Oracle native tools (e.g. Dataguard) to meet your HA and DR requirements. The following two are examples of such an architecture, the first targeting automatic failover and the second targeting manual failover.

### Example 1: Create a Fail-Over For Business Critical Oracle Applications in a multiple availability zone deployment and second region deployment for disaster recovery

Business-critical Oracle Applications require failure prevention and therefore holistic architecture. One of these business-critical applications can be Oracle E-Business Suite.

As a given Tier 1 example for Oracle E-Business Suite in a multiple availability zone deployment and second region deployments for disaster recovery.

- First establish a multiple availability zone deployment with separated VNet with subnets. The Application tier uses Azure Site Recovery with a passive secondary virtual machine in availability zone three from the availability zone 1 primary.
- Use two Oracle Observers as a primary in availability zone one and a secondary in availability zone two. The observers monitor and direct the whole traffic to the primary database. Whereas the primary database is deployed in availability zone one. Oracle Data Guard performs the redo sync to availability zone two and can be configured for maximum availability. Data Guard can be established as synchronous or asynchronous. Within one region a synchronous configuration can be used for reaching a lower latency as in async mode.

A second Data Guard standby configuration in the secondary region is established for disaster recovery purposes and is configured for maximum protection. Thereby backups of the database are performed by Azure Backup Volume Snapshot on Premium Files to the secondary region.

If a primary goes down, Observer(s) will reroute the traffic to the secondary DB2 as it comes out of standby, becomes primary and takes over all functionality for environment and sequence to fail over to secondary region standby if regional outage in first region.

![Alt text](image-1.png)

### Example 2: Create a Fail-Over For Business Critical Oracle Applications in a Two Availability Zone Deployment With Manual Failover

The web server tier, application tier and database tier reside in its own virtual network subnet.

Azure Site Recovery or the manual clone utility can be established to duplicate the passive secondary in AZ2. The primary will be set up in availability zone one whereas the database uses Data Guard to replicate it to an active Standby in AZ2.

A failover would require manual intervention from the system admin to fail over if there is a failure of availability zone one. Backups use Active Data Guard standby in AZ2 and backup to Azure Premium files in AZ2 to remove any additional IO pressure to the primary database.

![Alt text](image-2.png)

## Next Steps

> [!div class="nextstepaction"]
> [Security](./security-new.md)
