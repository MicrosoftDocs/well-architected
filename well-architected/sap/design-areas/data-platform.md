---
title: SAP workload data platform
description: SAP workload data platform
author: stephen-sumner
ms.author: ssumner
ms.date: 12/19/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload data platform

The data platform refers to the data store technologies that support an SAP workload. SAP workloads place high demands on the data platform. We outline best practices for optimizing cost while maintaining performance.

## Optimize data costs

We recommend optimizing the storage cost for your SAP workload. Storage is an essential component of an SAP workload. Storage contains active data and backup data that is critical to your organization. Storage affects the performance, availability, and recoverability of an SAP workload. It's important to have the right performance at the right cost. Here are recommendations to help you reach this goal.

**Use reserved capacity storage type.** There are several storage options available to choose from based on the workload requirement. Managed disks, blog storage, and backup storage can support an SAP workload in various combinations. Each of these options comes with storage reservation options that lower overall costs for persistent data. 

For more information, see:

- [Azure disk reserved capacity](/azure/virtual-machines/disks-reserved-capacity)
- [Blob storage reserved capacity](/azure/storage/blobs/storage-blob-reserved-capacity?toc=%2Fazure%2Fcost-management-billing%2Freservations%2Ftoc.json)
- [Azure Backup Storage reserved capacity](/azure/backup/backup-azure-reserved-pricing-optimize-cost)

**Use lifecycle management policies.** Other than reserved capacity, you need to ensure the data-retention period is right for the SAP workload. An SAP database backup can be large and add to the storage cost if not optimized. We recommend that you create a lifecycle policy that meets the recovery time objective (RTO) and recovery point objective (RPO) of your SAP workload. The policy should move into Premium, Standard, Cold, Archive storage based on its age and business requirements.

## Improve data reliability

Data reliability is essential for ensuring continuity of operations. We provide reliability recommendations for configuring database reliability, creating SAPMNT share reliability, using backups, and implementing a disaster recovery solution.

### Configure database reliability

An SAP application feeds data to multiple enterprise systems, making database resiliency a key workload consideration. We recommend replicating production data for the highest resiliency. Cross-region replication is the preferred disaster recovery solution. But for a more affordable option, you should configure zone redundancy at a minimum. The methods you choose depends on the database management system (DBMS) and required business service-level agreement (SLA). Below are recommendations for the database layer.

**Define RPO and RTO.** Creating database resiliency requires a plan to recover data loss. A logical error on the SAP database, a large-scale disaster, or a system outage can cause data loss in an SAP workload. Your recovery plan should identify how much data you’re willing to lose and how fast you need to recover. The amount of data loss you’re willing to lose is your recovery point objective (RPO). How fast you need to recover is your recovery time objective (RTO). When you design for recoverability, you need to understand the desired and actual RPO and RTO of your SAP application.

**Use synchronous replication for no data loss.**  In some scenarios, there’s no tolerance for data loss. The recovery point objective is 0. To achieve this RPO, you need use synchronous replication on the database layer. Synchronous replication commits database transactions to database instances in two separate zones or regions. You should measure the latency between the two instances to ensure it meets workload needs, and you can do it with the SAP `niping` measuring tool. Higher network latency will slow down the scalability of your workload, and physical distance between the instances adds network latency. As a result, replication across regions will have higher latency than across availability zones because there's more distance between the instances. Database replication between different regions should be asynchronous and replication between availability zones should be synchronous. It’s important to balance resiliency and latency in SAP workload design.

For more information, see:

- [General Azure Virtual Machines DBMS deployment for SAP workload]( /azure/virtual-machines/workloads/sap/dbms_guide_general)
- [High-availability architecture and scenarios for SAP NetWeaver](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Network latency between and within zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones#network-latency-between-and-within-zones)

### Create SAPMNT share reliability

SAPMNT hosts the physical kernel files for SAP application and can be a single point of failure. Several options are available on Azure to created redundancy and architect a highly available SAPMNT share. We recommend using Azure Premium Files or Azure NetApp Files for Linux and Azure Premium Files. For Windows-based deployments, you should use Azure NetApp Files or Azure Shared Disk.

There are also a few application specific configurations you should address for SAPMNT reliability. You need shared directories in the environment (`/sapmnt/SID and /usr/sap/trans`) to deploy the SAP NetWeaver application layer. We recommend creating highly available file systems and ensuring they're resilient. The `/sapmnt/SID` and `/usr/sap/SID/ASCS` directories are important. You should place these file systems on NFS on Azure Files to achieve the maximum reliability. 

For more information see, [NFS on Azure Files](/azure/storage/files/files-nfs-protocol).

*Table 1: SAPMNT guidance for each operating system.*

|OS | SAPMNT Guidance|
| --- | --- |
|Windows| [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk in Azure]( /azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) <br><br>[Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a file share in Azure]( /azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share) <br><br>[High availability for SAP NetWeaver on Azure VMs on Windows with Azure Files Premium SMB for SAP applications]( /azure/virtual-machines/workloads/sap/high-availability-guide-windows-azure-files-smb) <br><br>[High availability for SAP NetWeaver on Azure VMs on Windows with Azure NetApp Files(SMB) for SAP applications]( /azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)|
|Red Hat Enterprise Linux (RHEL) | [High availability for SAP NetWeaver on Azure VMs on Red Hat Enterprise Linux with NFS on Azure Files](/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-nfs-azure-files) <br><br> [Azure Virtual Machines high availability for SAP NetWeaver on Red Hat Enterprise Linux with Azure NetApp Files for SAP applications]( /azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
|SUSE Linux Enterprise Server (SLES) | [High-availability SAP NetWeaver with simple mount and NFS on SLES for SAP Applications VMs]( /azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs-simple-mount) <br><br>[High availability for SAP NetWeaver on Azure VMs on SUSE Linux Enterprise Server with NFS on Azure Files]( /azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs-azure-files)

### Use data backups

The SAP workload should implement a regular backup solution. Backups are the backbone of disaster recovery and help ensure continuity of operations. We have a few recommendations for backup reliability.

#### Start with Azure Backup

We recommend you use Azure Backup as the foundational backup strategy for an SAP workload. Azure Backup is the native backup solution in Azure, and it provides multiple capabilities to help streamline your SAP backups. With Azure Backup, we want to point out a few features.

**Native database backup compatibility.** Azure Backup provides native backups through the Backint connector for SAP HANA, SQL Server, and Oracle databases used by SAP Applications. Azure backup for SAP offers an API called Backint. Backint allows backup solutions to create backups directly on the database layer. Azure backup also supports the database backup capability for HANA & SQL Server databases today.

**Storage backup.** The storage backup feature can help optimize the backup strategy by using disk snapshots of Azure Premium storage for selective disks. For more information on application-consistent backups, see [snapshot consistency](/azure/backup/backup-azure-vms-introduction#snapshot-consistency).

**Virtual machine backup.** Back up and restore Azure VM data through the Azure portal. Cross-region restoration lets you restore Azure VMs that were to a paired secondary region.

**Long-term retention.** Azure Backup allows you to retain SAP backups years for compliance and audit needs.

**Backup Management.** Azure Backup enables you to manage backups from the Azure portal with an easy user interface.

For more information, see:

- [Azure Backup documentation]( /azure/backup/)
- [SAP HANA backup overview](/azure/backup/sap-hana-database-about)
- [Backup guide for SAP HANA on Azure Virtual Machines](/azure/backup/sap-hana-database-about)
- [Backup guide for SQL Server on Azure Virtual Machines](/azure/backup/tutorial-sql-backup)

#### Find marketplace backup solutions

Several certified third-party backup solutions exist in the [Azure Marketplace]( https://azuremarketplace.microsoft.com/). These solutions offer vendor backup capabilities and SAP-certified backup capabilities. You should consider layering these solutions on top of Azure Backup to generate custom solutions with foundational support.

Microsoft partners provide solutions that are integrated with Azure Storage for archive, backup, and for business continuity and disaster recovery (BCDR) workloads. The partner solutions take advantage of the scale and cost benefits of Azure Storage. You can use the solutions to help solve backup challenges, create a disaster recovery site, or archive unused content for long-term retention. They can replace tape-based backups and offer an on-demand economic recovery site with all the compliance standards and storage features such as [immutable storage]( /azure/storage/blobs/immutable-storage-overview) and [lifecycle management]( /azure/storage/blobs/lifecycle-management-overview).

#### Use snapshots

A snapshot is a point-in-time, copy of your data. The speed and reliability of snapshots can help manage large databases and protect the primary database against corruption or failure. These features make snapshots critical for disaster recovery. We have a few options to create and store backups for your SAP workload.

Azure Backup can take database backups for HANA and SQL Server, for example. The Backup vault feature of Azure Shared Disk can serve as your database storage solution. Azure NetApp Files (ANF) can also back up critical data by using snapshots, such as ANF volumes Snapshot. ANF Cross Region Replication uses ANF snapshots to replicate data from one region to another.

The right solution depends on your desired cost and availability levels. In some scenarios, you might want to replicate your SAP on Azure data to other Azure regions for disaster recovery. However, you can achieve the same capabilities with Azure Storage replication, such as Geo-redundant storage (GRS) or Azure Site Recovery. 

For more information, see:

- [SAP workload configurations with Azure Availability Zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)
- [SAP NetWeaver disaster recovery](/azure/site-recovery/site-recovery-sap)
- [Azure Site Recovery for SAP workloads](/azure/site-recovery/site-recovery-sap)
- [Azure Storage redundancy](/azure/storage/common/storage-redundancy)
- [Back up SAP HANA databases' instance snapshots in Azure VMs](/azure/backup/sap-hana-database-instances-backup)

### Implement a disaster recovery plan

We recommend you invest in disaster recovery (DR) to improve the reliability of the SAP workload. Disaster recovery is achieved by replicating primary data to a secondary location. Several tools & methodology can be used to the achieve goal. Disaster Recovery is required when the primary location isn't accessible due to technical or natural disaster. Disaster Recovery solutions can be across zones within region or across regions based on your business requirements, but we recommended DR across region for better resiliency.

For more information, see:

- [Azure Site Recovery](/azure/site-recovery/site-recovery-overview)
- [Cross-region replication of Azure NetApp Files volumes](/azure/azure-netapp-files/cross-region-replication-introduction)
- [Cross-region snapshot copy for Azure Disk Storage](/azure/virtual-machines/disks-incremental-snapshots?tabs=azure-cli#cross-region-snapshot-copy-preview)
- [Backup and Disaster Recovery](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview)

## Improve storage performance

It’s important to choose the appropriate storage solutions to support the data needs of the SAP workload. The correct solution can improve the performance of existing capabilities and allow you to add new features. In general, storage needs to meet the input/output operations per second (IOPS) requirements and throughput needs of the SAP database. 

For more information, see [storage types for an SAP workload](/azure/virtual-machines/workloads/sap/planning-guide-storage).

**Use storage that supports performance requirement.** Microsoft supports different storage technology to meet your performance requirement. For SAP workload, you can use Azure Managed Disk (for example, Premium SSD, Premium SSD v2, Standard SSD) and Azure NetApp Files.

**Configure storage for performance.** We've published a storage configuration guideline for SAP HANA databases. It covers production scenarios and a cost-conscious non-production variant. Following the recommended storage configurations will ensure the storage passes all SAP hardware and cloud measurement tool (HCMT) KPIs. For more information, see [SAP HANA Azure virtual machine storage configurations](/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).

**Enable write accelerator.** Write accelerator is a capability for M-Series VMs on Premium Storage with Azure Managed Disks exclusively. It’s imperative to enable write accelerator on the disks associated with the /hana/log volume. This configuration facilitates sub millisecond writes latency for 4 KB and 16-KB blocks sizes. For more information, see [Azure Write Accelerator](/azure/virtual-machines/how-to-enable-write-accelerator).

**Choose the right VM.** Choosing the right VM has cost and performance implications. The goal is to pick a storage VM that supports the IOPS and throughput requirements of the SAP workload. There are three critical areas to focus while selecting a VM

| Compute features | Description |
|---| --- |
|Number of vCPUs|The number of CPUs has a direct effect on the licenses in the database node. Most of the databases follow a core-based licensing model. Use the amount that meets your needs and adjust licensing agreements as necessary.|
|Memory|Memory is critical to application performance, and your SAP application can have high memory demands. In general, higher memory provides more memory-reads, less paging, and higher VM cost.|
|Throughput|Throughput is important for an application hosted on one of the VMs to communicate with outside the VM by using its network interface cards (NICs).|

## Next steps

> [!div class="nextstepaction"]
> [Application design](./application-design.md)

> [!div class="nextstepaction"]
> [Application platform](./application-platform.md)

> [!div class="nextstepaction"]
> [Networking and connectivity](./networking-and-connectivity.md)

> [!div class="nextstepaction"]
> [Security](./security.md)

> [!div class="nextstepaction"]
> [Operational procedures](./operational-procedures.md)
