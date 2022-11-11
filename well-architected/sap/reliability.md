---
title: SAP workload reliability
description: SAP workload best practices for reliability
author: stephen-sumner
ms.author: ssumner
ms.date: 11/14/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload reliability

A reliable SAP workload is both resilient and available. Resiliency is the ability to recover from failures and continue to function. Availability is uptime. High availability reduces SAP application downtime during critical maintenance and improves recovery from failures such as VM crashes, backend updates, major downtime, or ransomware incidents. Failures happen on-premises and in the cloud, so it’s important to design your SAP workload for resiliency and availability. Below we’ve outlined key reliability recommendations.

## Conduct a reliability assessment

Before you can standardize the reliability of an SAP workload and improve areas of weakness, you need to assess its reliability. It’s critical to know how reliable an SAP workload is so steps can be taken to fix issues or solidify those configurations. We recommend conducting a reliability assessment on your SAP workload. The assessment asks you questions about your workload and provides specific recommendations to focus on. The assessment builds on itself, so you can track your progress without restarting every time. For the assessment, start an [Azure Well-Architected Review](/assessments/) and select “SAP on Azure” when prompted.

## Create architecture reliability

Creating a multi-tier architecture to support an SAP workload is essential for reliability. The number of tiers and architecture varies for each SAP application.  Make sure to isolate application components from each other and create redundancy to achieve high availability. Where applicable, you should isolate the SAP Web Dispatcher, SAP Central Services, SAP App Server, SAPMNT Share and database. We have sample architectures for several different SAP applications you can use to inform your design. For more information, see:

- [SAP S/4HANA in Linux](/azure/architecture/guide/sap/sap-s4hana)
- [SAP BW/4HANA](/azure/architecture/reference-architectures/sap/run-sap-bw4hana-with-linux-virtual-machines)
- [SAP NetWeaver](/azure/architecture/guide/sap/sap-netweaver)

## Create SAP central services reliability

SAP central services (SCS) or ABAP SAP central services (ASCS) is the basis of SAP application communication. It consists of the message server and enqueue server. The central services layer is often a single point of failure and must be set up for high availability to achieve SAP application resiliency. To add redundancy, create a cluster of SAP central services with compatible shared storage technology supporting the cluster. Depending on the operating system and available shared storage technology in general availability or private/public preview, various options are available. Availability zones provide an opportunity to create a highly available ASCS architecture. For more information, see:

- [SAP workload configurations with Azure Availability Zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)
- [High-availability architecture for an SAP ASCS/SCS instance on Windows](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-an-sap-ascsscs-instance-on-windows)
- [High-availability architecture for an SAP ASCS/SCS instance on Linux](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-an-sap-ascsscs-instance-on-linux)

## Create SAPMNT share reliability

SAPMNT hosts the physical kernel files for SAP application and can be a single point of failure. Several options are available on Azure to created redundancy and architect a highly available SAPMNT share. We recommend using Azure Premium Files or Azure NetApp Files for Linux and Azure Premium Files. For Windows-based deployments, you should use Azure NetApp Files or Azure Shared Disk.

There are also a few application specific configurations you should address for SAPMNT reliability. You need shared directories in the environment (`/sapmnt/SID and /usr/sap/trans`) to deploy the SAP NetWeaver application layer. We recommend creating highly available file systems and ensuring they're resilient. The `/sapmnt/SID` and `/usr/sap/SID/ASCS` directories are important. You should place these file systems on NFS on Azure Files to achieve the maximum reliability. For more general file share information see, [NFS on Azure Files](/azure/storage/files/files-nfs-protocol).

The table below provides SAPMNT guidance specific to each operating system.

|OS | Guidance|
| --- | --- |
|Windows| - [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk in Azure]( /azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) <br><br> - [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a file share in Azure]( /azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share) <br><br> - [High availability for SAP NetWeaver on Azure VMs on Windows with Azure Files Premium SMB for SAP applications]( /azure/virtual-machines/workloads/sap/high-availability-guide-windows-azure-files-smb) <br><br> - [High availability for SAP NetWeaver on Azure VMs on Windows with Azure NetApp Files(SMB) for SAP applications]( /azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)|
|Red Hat Enterprise Linux (RHEL) | - [High availability for SAP NetWeaver on Azure VMs on Red Hat Enterprise Linux with NFS on Azure Files](/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-nfs-azure-files) <br><br> - [Azure Virtual Machines high availability for SAP NetWeaver on Red Hat Enterprise Linux with Azure NetApp Files for SAP applications]( /azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
|SUSE Linux Enterprise Server (SLES) |- [High-availability SAP NetWeaver with simple mount and NFS on SLES for SAP Applications VMs]( /azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs-simple-mount) <br><br> - [High availability for SAP NetWeaver on Azure VMs on SUSE Linux Enterprise Server with NFS on Azure Files]( /azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs-azure-files)

## Create database resiliency

An SAP application feeds data to multiple enterprise systems, making database resiliency a key workload consideration. We recommend replicating production data for the highest resiliency. Cross-region replication is the preferred disaster recovery solution. But for a more affordable option, you should configure zone redundancy at a minimum. The methods you choose depends on the database management system (DBMS) and required business service-level agreement (SLA). Below are recommendations for the database layer.

**(1) Define RPO and RTO** - Creating database resiliency requires a plan to recover data loss. A logical error on the SAP database, a large-scale disaster, or a system outage can cause data loss in an SAP workload. Your recovery plan should identify how much data you’re willing to lose and how fast you need to recover. The amount of data loss you’re willing to lose is your recovery point objective (RPO). How fast you need to recover is your recovery time objective (RTO). When you design for recoverability, you need to understand the desired and actual RPO and RTO of your SAP application.

**(2) Use synchronous replication for no data loss** - In some scenarios, there’s no tolerance for data loss. The recovery point objective is 0. To achieve this RPO, you need use synchronous replication on the database layer. Synchronous replication commits database transactions to database instances in two separate zones or regions. You should measure the latency between the two instances to ensure it meets workload needs, and you can do it with the SAP `niping` measuring tool. Higher network latency will slow down the scalability of your workload, and physical distance between the instances adds network latency. As a result, replication across regions will have higher latency than across availability zones. Database replication between different regions should be asynchronous and replication between availability zones should be synchronous. It’s important to balance resiliency and latency in SAP workload design. For more information, see:

- [General Azure Virtual Machines DBMS deployment for SAP workload]( /azure/virtual-machines/workloads/sap/dbms_guide_general)
- [High-availability architecture and scenarios for SAP NetWeaver](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Network latency between and within zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones#network-latency-between-and-within-zones)

## Create application server resiliency

The goal of application server resiliency is to have multiple application servers load balance traffic and failover when needed. Resiliency for the SAP application server layer can be achieved through redundancy. You can configure multiple dialog instances on different instances of Azure virtual machines with a minimum of two application servers. Here are application server resiliency recommendations.

**(1) Use Availability Sets / Availability Zones** - An SAP application server can be deployed in an availability set or across availability zones. The decision you make needs to be based on workload requirements. We recommend you choose one method to improve resiliency, but we don’t recommend scale sets. For more information, see [availability zones for SAP](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones).

**(2) Use multiple application servers** - Using multiple smaller application servers instead of one larger application server is recommended. This setup avoids a single point of failure. It’s a best practice to configure SAP Logon Group (SMLG) and Batch Server Group (RZ12) for better load balancing between end-user & batch processing. For more information, see:

- [Azure Virtual Machines high availability for SAP NetWeaver](/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)
- [SAP HANA high availability for Azure virtual machines](/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP workload configurations with Azure Availability Zones]( /azure/virtual-machines/workloads/sap/sap-ha-availability-zones)

## Use backups

The SAP workload should implement a regular backup solution. Backups are the backbone of disaster recovery and help ensure continuity of operations. We have a few recommendations for backup reliability.

**(1) Start with Azure Backup** - We recommend you use Azure Backup as the foundational backup strategy for an SAP workload. Azure Backup is the native backup solution in Azure, and it provides multiple capabilities to help streamline your SAP backups. With Azure Backup, we want to point out a few features.

*Native database backup compatibility* - Azure Backup provides native backups through the Backint connector for SAP HANA, SQL Server, and Oracle databases used by SAP Applications. Azure backup for SAP offers an API called Backint. Backint allows backup solutions to create backups directly on the database layer. Azure backup also supports the database backup capability for HANA & SQL Server databases today.

*Storage backup* - The storage backup feature can help optimize the backup strategy by using disk snapshots of Azure Premium storage for selective disks. For more information on application-consistent backups, see [snapshot consistency](/azure/backup/backup-azure-vms-introduction#snapshot-consistency).

*Virtual Machine backup* - Back up and restore Azure VM data through the Azure portal. Cross-region restoration lets you restore Azure VMs that were to a paired secondary region.

*Long-term retention* - Azure Backup allows you to retain SAP backups years for compliance and audit needs.

*Backup Management* - Azure Backup enables you to manage backups from the Azure portal with an easy user interface.

For more information, see:

- [Azure Backup documentation]( /azure/backup/)
- [SAP HANA backup overview](/azure/backup/sap-hana-database-about)
- [Backup guide for SAP HANA on Azure Virtual Machines](/azure/backup/sap-hana-database-about)
- [Backup guide for SQL Server on Azure Virtual Machines](/azure/backup/tutorial-sql-backup)

**(2) Find marketplace solutions** - Several certified third-party backup solutions exist in the [Azure Marketplace]( https://azuremarketplace.microsoft.com/). These solutions offer vendor backup capabilities and SAP-certified backup capabilities. You should consider layering these solutions on top of Azure Backup to generate custom solutions with foundational support.

Microsoft partners provide solutions that are integrated with Azure Storage for archive, backup, and for business continuity and disaster recovery (BCDR) workloads. The partner solutions take advantage of the scale and cost benefits of Azure Storage. You can use the solutions to help solve backup challenges, create a disaster recovery site, or archive unused content for long-term retention. They can replace tape-based backups and offer an on-demand economic recovery site with all the compliance standards and storage features such as [immutable storage]( /azure/storage/blobs/immutable-storage-overview) and [lifecycle management]( /azure/storage/blobs/lifecycle-management-overview).

**(3) Use snapshots** - A snapshot is a point-in-time, copy of your data. The speed and reliability of snapshots can help manage large databases and protect the primary database against corruption or failure. These features make snapshots critical for disaster recovery. We have a few options to create and store backups for your SAP workload.

Azure Backup can take database backups for HANA and SQL Server, for example. The Backup vault feature of Azure Shared Disk can serve as your database storage solution. Azure NetApp Files (ANF) can also back up critical data by using snapshots, such as ANF volumes Snapshot. ANF Cross Region Replication uses ANF snapshots to replicate data from one region to another.

The right solution depends on your desired cost and availability levels. In some scenarios, you might want to replicate your SAP on Azure data to other Azure regions for disaster recovery. However, you can achieve the same capabilities with Azure Storage replication, such as Geo-redundant storage (GRS) or Azure Site Recovery. For more information, see:

- [SAP workload configurations with Azure Availability Zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)
- [SAP NetWeaver disaster recovery](/azure/site-recovery/site-recovery-sap)
- [Azure Site Recovery for SAP workloads](/azure/site-recovery/site-recovery-sap)
- [Azure Storage redundancy](/azure/storage/common/storage-redundancy)
- [Back up SAP HANA databases' instance snapshots in Azure VMs](/azure/backup/sap-hana-database-instances-backup)

**(4) Implement Disaster Recovery** - We recommend you invest in Disaster Recovery (DR) to improve the reliability of the SAP workload. Disaster recovery is achieved by replicating primary data to a secondary location. Several tools & methodology can be used to the achieve goal. Disaster Recovery is required when the primary location isn't accessible due to technical or natural disaster. Disaster Recovery solutions can be across zones within region or across regions based on your business requirements, but we recommended DR across region for better resiliency. For more information, see:

- [Azure Site Recovery](/azure/site-recovery/site-recovery-overview)
- [Cross-region replication of Azure NetApp Files volumes](/azure/azure-netapp-files/cross-region-replication-introduction)
- [Cross-region snapshot copy for Azure Disk Storage](/azure/virtual-machines/disks-incremental-snapshots?tabs=azure-cli#cross-region-snapshot-copy-preview)
- [Backup and Disaster Recovery](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview)

## Next step

>[!div class="nextstepaction"]
>[Overview](./overview.md)

>[!div class="nextstepaction"]
>[Security](./security.md)

>[!div class="nextstepaction"]
>[Cost Optimization](./cost-optimization.md)

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)
