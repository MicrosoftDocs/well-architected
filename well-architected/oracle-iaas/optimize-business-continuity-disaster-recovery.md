---
title: Optimize business continuity and disaster recovery
description: Review the design area for BCDR of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: jhaessler 
ms.date: 07/30/2024
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-workload-oracle
---

# Optimize business continuity and disaster recovery

When you migrate Oracle resources to Azure, consider the reliability of your database and also the reliability of the tiers on virtual machines (VMs), virtual network subnets, and storage components.

Oracle on Azure infrastructure as a service (IaaS) can fulfill the required resiliency objectives of the most demanding Oracle workloads. To effectively use the guidance in this article, first define your resiliency key performance indicators (KPIs) based on your business requirements. Use your recovery time objective (RTO) and recovery point objective (RPO) requirements as baseline KPIs to determine the best architecture for your Oracle workload on Azure.

The *RTO* is the maximum amount of time that an application remains unavailable after a disaster, failure, or comparable event.

The *RPO* is the maximum amount of data loss after a disaster, failure, or comparable event.

## Backup methods for data protection

The three Oracle database backup methods for an Oracle workload on Azure IaaS include:

- *Streaming backups*. Use Oracle Recovery Manager (RMAN) for this method. RMAN streams backups to sequential tape media.
   
   Backup destinations on Azure include: 
   - Non-Microsoft virtual tape libraries, which you can find in Azure Marketplace.
   - Local and remote file shares, such as Azure Blob Storage with the Network File System protocol, Azure Files, and Azure NetApp Files.

- *Storage-level snapshots*. Use Azure Backup for this method. This method relies on the type of storage that you use for database files. For example, if you use Azure managed disks, such as Azure Premium SSD, [Azure Backup integrates with the Oracle database](/azure/virtual-machines/workloads/oracle/oracle-database-backup-azure-backup). If you use Azure NetApp Files, you can use Azure NetApp Files data protection capabilities, like [Azure NetApp Files backup](/azure/azure-netapp-files/backup-introduction) and [cross-region replication](/azure/azure-netapp-files/cross-region-replication-introduction).
- *VM-level backups*. Use [Azure Backup](/azure/virtual-machines/workloads/oracle/oracle-database-backup-azure-backup) for this method.

  >[!Caution]
  >Make sure the VMs in your backup environment are running OSs that have supportability. [Learn about the supported OSs](/azure/virtual-machines/enable-nvme-interface).

When you stream backups of large databases, the time that it takes to copy the data to then restore it can exceed the RTO requirements. Storage-level snapshots are the best option for that scenario.

### Recommendations

- Carefully consider whether to implement a backup strategy that's based on streaming, on storage-level snapshots, or on both strategies.

- Assess the effect of your backup strategy on your RTO and RPO requirements.
- Analyze the available storage destinations for your RMAN backups based on the documented throughput limits for each option. Choose the option that meets your requirements.
- Consider using Azure Backup for your storage-level snapshots, and consider placing the snapshots in a paired region or an availability zone for extra protection.
- Consider various storage options to store the archive log backups that you need to recover the database. Consider the performance, replication, and cost considerations of each option. 
- Develop and regularly test your backup and restore plans to prevent unwanted surprises in your production environment.

## Service protection and business continuity

This section describes how to improve the overall high availability (HA) and disaster recovery (DR) of your Oracle workload on Azure IaaS by implementing service protection and business continuity (BC) considerations.

Incorporate the following recommendations to improve architectural redundancy and, ultimately, maximize the amount of time that your service is available. Aim to minimize service downtime due to planned outages, such as patches, updates, and upgrades, and unplanned outages, such as failures. Use Azure and Oracle capabilities to improve your recovery from geography-wide failures.

Azure provides many options for the high availability of individual components in an Oracle on IaaS architecture. For example, you can:

- Deploy VMs by using a flexible virtual machine scale set, which automatically spreads VMs across fault domains.
- Create availability zones to protect against datacenter failures.
- Place deployments in different regions to protect against full-region failures.

Various Azure storage capabilities provide different storage redundancy levels, such as locally redundant storage, zone-redundant storage, and geo-redundant storage. Consider each option when you plan your Oracle workload deployment on Azure IaaS.

You can also use [Oracle Data Guard](/azure/virtual-machines/workloads/oracle/configure-oracle-DataGuard), which is a tool for Oracle database service protection setups. Data Guard forwards and applies transaction logs to one or more standby databases. This process maintains exact copies of the primary database that you can fail over to if you have planned maintenance or a failure scenario.

Data Guard has three modes of data replication: maximum protection, maximum availability, and maximum performance. Each replication mode offers a different combination of log transport modes and different transactional guarantees for the application on the secondary database. 

Depending on your strategy, such as a zero-latency or zero data-loss strategy, you can choose a synchronous or an asynchronous configuration.
You can also implement fast-start failover, depending on your maximum downtime requirements. Reference architectures are available that provide a recovery in less than one minute or less than five minutes, and up to four hours. The Enterprise Edition of Oracle Database includes Data Guard.

[Oracle GoldenGate](/azure/virtual-machines/workloads/oracle/configure-oracle-golden-gate) is another tool that you can use to replicate data between two databases and enable multi-primary scenarios. You must purchase GoldenGate separately.

### Recommendations

- Consider the capabilities that Azure provides for the high availability of various infrastructure components in your Oracle on Azure IaaS implementation.

- Carefully select the database protection mode that fulfills your requirements when you use Data Guard for HA and DR. For example, maximum performance mode minimizes the impact on the source but has the highest potential for data loss. For more information, see [BCDR for Oracle on Azure Virtual Machines landing zone accelerator](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-disaster-recovery-oracle-landing-zone) and [Oracle Data Guard protection modes](https://docs.oracle.com/en/database/oracle/oracle-database/21/sbydb/oracle-data-guard-protection-modes.html#GUID-7EF6EFEE-7E31-4F80-9C97-1B25DAA025F8).
- Consider automating your failover process. For example, you can use fast-start failover.
- Establish test procedures for your failover processes, and carry out regular testing to avoid any problems.
- Architect your solution holistically by using Azure-native capabilities, like availability zones, and Oracle-native tools, like Data Guard, to meet your HA and DR requirements. The following two examples use Azure-native and Oracle-native components.
- Manage your business continuity and disaster recovery protection across solutions and environments with [Azure Business Continuity Center](/azure/business-continuity-center/business-continuity-center-overview). This Azure solution provides a single pane of glass that enables you to manage, monitor, and review reports periodically for the protection and recovery of all workloads.


### Create a failover with passive standby

This section describes an example of a failover scenario for business-critical Oracle applications in a two-availability zone deployment with passive standby.

Business-critical Oracle applications, such as Oracle E-Business Suite, require failure prevention and therefore a holistic architecture.

This example:

- Has a two-availability zone deployment. The application tier uses Azure Site Recovery with a passive secondary VM.

- Takes advantage of the Data Guard fast-start failover feature. To get the highest availability, we recommend that you install two observers. The primary observer is in availability zone one, and the secondary observer is in availability zone two. The observers monitor and direct traffic. When the primary database is unavailable, the observer automatically fails over to the secondary database. Data Guard performs a redo sync. The time frame of the redo sync depends on your redo configuration.
   
- Has Data Guard configured to [a data protection mode](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/oracle-data-guard-protection-modes.html#GUID-5DB32C5F-3ABF-4AD4-AB41-208F1BF134BB), such as maximum availability, maximum performance, or maximum protection. For more information about choosing a mode for your workload requirements, see [Oracle Data Guard protection modes](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/oracle-data-guard-protection-modes.html#GUID-7EF6EFEE-7E31-4F80-9C97-1B25DAA025F8).


The following architecture aims for a downtime threshold of less than five minutes. 

:::image type="content" source="./images/optimize-business-continuity-disaster-recovery/active-passive.svg" alt-text="Diagram that shows the architecture for a failover with passive standby." lightbox="./images/optimize-business-continuity-disaster-recovery/active-passive.svg" border="false":::

### Create a failover with active standby

This section describes an example of a failover scenario for business-critical Oracle applications in a two-availability zone deployment with active standby.

In this example: 

- The web server tier, application tier, and database tier reside in their own virtual network subnet.

- The primary database resides in availability zone one.
- The database that uses Active Data Guard to replicate the primary database to an active standby resides in availability zone three.

> [!NOTE]
> This setup requires an Active Data Guard license.

The following architecture aims for a downtime threshold of less than one minute. This failover scenario has an active standby configuration but has read-only capabilities.

:::image type="content" source="./images/optimize-business-continuity-disaster-recovery/active-active.svg" alt-text="Diagram that shows the architecture for a failover with active standby." lightbox="./images/optimize-business-continuity-disaster-recovery/active-active.svg" border="false":::

## Next step

> [!div class="nextstepaction"]
> [Optimize security](optimize-security.md)
