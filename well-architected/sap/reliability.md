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

A reliable SAP workload is one that is both resilient and available. Resiliency is the ability of the SAP workload to recover from failures and continue to function. The goal of resiliency is to return the SAP application to a fully functioning state after a failure occurs. When you design an SAP workload, it’s important to consider availability and recoverability. Failures are bound to happen whether it's on-premises or cloud. A highly available architecture reduces SAP application downtime during critical maintenance and improves recovery from failures such as VM crashes, backend updates, major downtime, or ransomware incidents. Reliability helps ensure that workloads are available and can recover from failures at any scale.

Here are a few recommendations to consider for your SAP workload.

## Conduct a reliability assessment

Before you can standardize the reliability of an SAP workload and improve areas of weakness, you need to assess its reliability. It’s critical to know how reliable an SAP workload is so steps can be taken to standardize reliability and improve areas of weakness. For the assessment, see [Azure Well-Architected Review](/assessments) and select SAP on Azure when prompted.

## Create architecture resiliency

Creating a multi-tier architecture to support an SAP workload is essential for reliability. The number of tiers and architecture specifics varies by SAP application.  Make sure to isolate all application components from each other to achieve high availability through redundancy. Where applicable, you should isolate the SAP Web Dispatcher, SAP Central Services, SAP App Server, and SAP database.
We have sample architectures for several different SAP applications you can use to inform your design.

For more information, see:

- [SAP S/4HANA in Linux](/azure/architecture/guide/sap/sap-s4hana)
- [SAP BW/4HANA](/azure/architecture/reference-architectures/sap/run-sap-bw4hana-with-linux-virtual-machines)
- [SAP NetWeaver](/azure/architecture/guide/sap/sap-netweaver)

## Create application server resiliency

Application server resiliency is critical to ensure there are multiple application servers to load balance the load if there was single application server failure. Resiliency for the SAP application server layer can be achieved through redundancy. You can configure multiple dialog instances on different instances of Azure virtual machines with a minimum of two application servers.

**(1) Use Availability Sets** - We recommend deploying the SAP application servers in the same Availability Set. We don’t recommend scale sets. The Availability Set ensures the application servers are in different fault domains. Different fault domains avoid shared physical layers between the Azure virtual machines and limit the effects of physical hardware failures, network outages, or power interruptions.

**(2) Use multiple smaller servers** - Using multiple smaller servers instead of one larger application server avoids a single point of failure. It’s a best practice is to configure the guest operating system's cluster technologies, such as Windows Failover Cluster or Linux Pacemaker to reduce failover times of the SAP Central Services and database management system (DBMS).

For more information, see:

- [High-availability architecture for SAP application servers](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-sap-application-servers)
- [NetWeaver high availability](/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)
- [SAP HANA high availability](/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Overview of Availability Sets](/azure/virtual-machines/availability#availability-sets)

## Create SAP central services resiliency

SAP central services (SCS) or ABAP SAP central services (ASCS) is the basis of SAP application communication. It consists of the message server and enqueue server. The central services layer is often a single point of failure and must be set up high-available to achieve SAP application resiliency. To add redundancy, create a cluster of SAP central services with compatible shared storage technology supporting the cluster. Depending on the operating system and available shared storage technology in General Availability (GA) or Private/Public Preview, various options are available. Availability zones provide an opportunity to create a highly available ASCS architecture.

For more information, see:

- [SAP workload configurations with Azure Availability Zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)
- [High-availability architecture for an SAP ASCS/SCS instance on Windows](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-an-sap-ascsscs-instance-on-windows)
- [High-availability architecture for an SAP ASCS/SCS instance on Linux](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-an-sap-ascsscs-instance-on-linux)

## Create database resiliency

An SAP application feeds data to multiple enterprise systems, making database resiliency a key workload consideration. We recommend replicating production data across regions for the highest resiliency. Cross-region replication is the preferred disaster recovery solution. For a more affordable option, you should configure zone redundancy at minimum. For more information, see [availability zones for SAP](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones).

On the database layer, you can replicate production data within the region or between primary and disaster recovery region. The replication should be synchronous to provide high availability and synchronous to support disaster recovery. You can use different database methods. The methods you choose depends on the database management system (DBMS) and required business service-level agreement (SLA).

Here are our recommendations when you design the database layer:

**(1) Define RPO and RTO** - Creating database resiliency requires a plan to recover data loss. There are numerous potential causes of data loss in an SAP workload. These causes include logical error on the SAP database, a large-scale disaster, or a system outage. Your recovery plan should identify how much data you’re willing to lose and how fast you need to recover. The amount of data loss you’re willing to lose is your recovery point objective (RPO). How fast you need to recover is your recovery time objective (RTO). When you design for recoverability, you need to understand the desired and actual RPO and RTO of your SAP application.

**(2) Use of synchronous replication for no data loss** - In some scenarios, there’s no tolerance for data loss. The recovery point objective is 0, and you need synchronize data. We recommend that you use synchronous replication on the database layer to create a high-availability configuration within a given region. Synchronous replication creates database transactions to database instances in two separate zones or regions. The latency between the two instances needs to be measured. Higher network latency slows down the scalability of your workload. More physical distance between the instances increases network latency. Synchronous replication across regions will have higher latency than across availability zones. As a result, database replication between different regions is asynchronous and replication between availability zones is synchronous. It’s important to balance resiliency and latency in SAP workload design.

For more information, see [General Azure Virtual Machines DBMS deployment for SAP workload](/azure/virtual-machines/workloads/sap/dbms_guide_general).

## Use backups

The SAP workload should implement a regular backup solution. Backups are the backbone of disaster recovery and help ensure continuity of operations. For more information, see [NetWeaver disaster recovery](/azure/site-recovery/site-recovery-sap).

**(1) Start with Azure Backup** - Azure Backup is the native backup solution in Azure. We recommend you use Azure Backup as the foundational backup strategy for an SAP workload. It provides multiple capabilities to help streamline your SAP backups:

- ***Native database backup compatibility*** - Azure Backup provides native backups through the Backint connector for SAP HANA, SQL Server, Oracle databases used by SAP Applications. Azure backup for SAP offers an API called Backint. Backint allows backup solutions to create backups directly on the database layer. Azure backup also supports the DB Snapshot backup capability for HANA & SQL Server databases.
- ***Storage backup*** - The storage backup feature can help optimize the backup strategy by using disk snapshots of Azure Premium storage for selective disks. For more information on application-consistent backups, see [snapshot consistency] [/ azure/backup/backup-azure-vms-introduction#snapshot-consistency].
- ***Virtual Machine backup*** - Back up and restore of Azure VM data through the Azure portal. Cross-region restoration allows you to restore Azure VMs in a secondary region.
- ***Long-term retention*** - Azure backup allows you to retain SAP backups years for compliance and audit needs.
- ***Backup Management*** - Azure Backup enables you to manage backups from the Azure portal with an easy user interface.

For more information, see:

- [Azure Backup service documentation](/azure/backup/)
- [Backup guide for SAP HANA on Azure VMs](/azure/backup/sap-hana-database-about)
- [SAP HANA backup overview](/azure/backup/sap-hana-database-about)

**(2) Find marketplace solutions** - Several certified third-party backup solutions exist in the Azure Marketplace. These solutions offer vendor- and SAP-certified backup capabilities. You should consider layering these solutions on top of Azure Backup capabilities for a customized backup strategy that meets your needs.

Microsoft partners provide solutions that are integrated with Azure Storage for archive, backup, and for business continuity and disaster recovery (BCDR) workloads. The partner solutions take advantage of the scale and cost benefits of Azure Storage. You can use the solutions to help solve backup challenges, create a disaster recovery site, or archive unused content for long-term retention. They can replace tape-based backups and offer an on-demand economic recovery site with all the compliance standards and storage features such as immutable storage and lifecycle management.

**(3) Use snapshots** -  Storage solutions such as Azure NetApp Files can back up critical data by using snapshots. When you decide which redundancy option is best for your scenario, consider the trade-offs between lower costs and higher availability. In some scenarios, you might want to replicate your SAP on Azure data to other Azure regions for disaster recovery. However, you can achieve the same capabilities with Azure Storage replication, such as Geo-redundant storage (GRS) or Azure Site Recovery.

For more information, see:

- [SAP workload configurations with Azure Availability Zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)
- [Azure Site Recovery for SAP workloads](/azure/site-recovery/site-recovery-sap)
- [Azure storage redundancy](/azure/storage/common/storage-redundancy)

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
