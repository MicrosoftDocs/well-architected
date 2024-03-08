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

There are three possible types of Oracle database backup that are relevant when the Oracle workload is implemented on Azure IaaS:
- Streaming backups using Oracle RMAN (Recovery Manager) which is originally based on backups being streamed to sequential tape media. This type of backup typically has two possible destinations on Azure: Virtual Tape libraries from third party providers on the Azure marketplace, or local and remote Fileshares such as Azure Blob NFS, Azure Files and Azure NetApp Files.
- Storage-level snapshots typically using Azure backup service. This backup method depends on the type of storage used for database files. For example, for Azure Managed disk (premium SSD) [Azure Backup is integrated with the Oracle database](/azure/virtual-machines/workloads/oracle/oracle-database-backup-azure-backup?tabs=azure-portal) while for Azure NetApp Files there are data protection capabilities such as [ANF backup](h/azure/azure-netapp-files/backup-introduction) and [cross-region replication](/azure/azure-netapp-files/cross-region-replication-introduction).
- VM-level Backups can be executed through [Azure Backup](/azure/virtual-machines/workloads/oracle/oracle-database-backup-azure-backup?tabs=azure-portal)

It is important to note that with streaming backups of large databases, the time taken to copy the data back in order to restore it can exceed the RTO requirements. In that case the storage-level snapshots become the better option.

### Assessment questions
- Are RPO & RTO requirements defined?
- Is the retention period defined (frequency, increments and targets)?
- Have you implemented and tested (or do you have plans to implement and test) backup strategies for your Oracle on Azure IaaS deployment?

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

In addition, Oracle provides [Dataguard](/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard), which is the most common tool used for Oracle database service protection setups. It works by forwarding and applying transaction logs to one or more standby databases to maintain exact copies of the primary database to fail over to, either as part of planned maintenance or in a failure scenario. Dataguard has three modes of data replication (Maximum Protection, Maximum Availability and Maximum Performance) which offer different combinations of log transport modes and guarantees for transactions application on the secondary database. Depending on a zero-latency or zero data loss strategy, a synchronous or asynchronous configuration can be chosen.
Depending on your maximum downtime requirements, Fast Start Failover (FSFO) can be implemented. Reference architectures are available from lesser than 1 minute recovery to lesser than 5 minutes up to 4 hours. 

Oracle [Goldengate](/azure/virtual-machines/workloads/oracle/configure-oracle-golden-gate) is another tool that can replicate data between two databases and can enable multi master scenarios. Goldengate is an extra-cost option as compared with Dataguard which is integrated into Oracle Enterprise Edition.

### Assessment question
- Do you have a disaster recovery plan/solution in place? 
- Have you implemented and tested (or do you have plans to implement and test) HA and DR strategies for your Oracle on Azure IaaS deployment?
- Did you define your maximum downtime requirements?

### Recommendations
- Take into consideration the capabilities that Azure provides for the High Availability of the different Infrastructure components in your Oracle on IaaS implementation.
- When using Dataguard for HA and DR, carefully select the database protection mode that fulfills your requirements. For example using Maximum Performance mode provides the minimum impact on the source but the highest potential for data loss. Please also review Business continuity and disaster recovery [(BCDR) for Oracle on Azure Virtual Machines landing zone accelerator](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-disaster-recovery-oracle-landing-zone) and [Oracle Data  Guard Protection Modes](https://docs.oracle.com/en/database/oracle/oracle-database/21/sbydb/oracle-data-guard-protection-modes.html#GUID-7EF6EFEE-7E31-4F80-9C97-1B25DAA025F8)
- Consider automating your failover process e.g. using Fast-start Failover.
- Establish test procedures for your failover processes and carry out regular testing to avoid any issues.
- Architect your solution holistically by utilizing the Azure native capabilities (e.g. Availability Zones) and Oracle native tools (e.g. Dataguard) to meet your HA and DR requirements. The following two are examples of such an architecture, the first targeting automatic failover and the second targeting manual failover.

### Example 1: Create a Fail-Over For Business Critical Oracle Applications in a Two Availability Zone Deployment With Passive Standby

Business-critical Oracle Applications require failure prevention and therefore holistic architecture. One of these business-critical applications can be Oracle E-Business Suite.

As a given Tier 1 example for Oracle E-Business Suite in a multiple availability zone deployment and second region deployments for disaster recovery.

- First establish a two availability zone deployment. The application tier uses Azure Site Recovery with a passive secondary virtual machine.
- Use two Oracle Observers as a primary in availability zone one and a secondary in another availability zone. The observers monitor and direct the whole traffic to the primary database. Whenever the primary is unavailable Oracle Data Guard performs the redo sync to the secondary availability zone. Data Guard needs to be configured with a [data protection mode](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/oracle-data-guard-protection-modes.html#GUID-5DB32C5F-3ABF-4AD4-AB41-208F1BF134BB). 
For further assistance on choosing the mode for your workload requirements, please review [Azure Landing Zone for Oracle Workloads BCDR](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-migration-planning).


The following architecture aims for a downtime threshold of less than 5 minutes. 

![Active-Passive Architecture on Azure](active-passive.png)

### Example 2: Create a Fail-Over For Business Critical Applications in a Two Availability Zone Deployment With Active Standby

The web server tier, application tier and database tier reside in its own virtual network subnet.

The primary will be set up in availability zone one whereas the database uses Active Data Guard to replicate it to an active Standby in AZ3.
The following diagram aims for a downtime threshold of less than 1 minutes. Even though it is an Active Standby, keep in mind that this standby has 'read-only' capabilties.

**Note that this setup requires an Active Data Guard license.**


![Active-Active Architecture on Azure](active-active.png)

## Next Steps

> [!div class="nextstepaction"]
> [Security](./security-new.md)
