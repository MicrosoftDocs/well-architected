---
title: Data management for reliability
description: Learn about database resiliency and advantages of distributing data geographically for reliability, including storage resiliency samples.
author: martinekuan
ms.author: martinek
ms.date: 04/28/2023
ms.topic: conceptual
products:
  - azure-sql-database
ms.custom:
  - How are you managing your data?
  - article
---

# Data management for reliability

This article describes database resiliency and database recovery by using geo-restore and active geo-replication. Use the samples described here to understand storage resiliency.

## Database resiliency

Azure services offer resiliency, including Azure SQL Database, SQL Server on virtual machines, and Azure Cosmos DB.

### Azure SQL Database

SQL Database automatically performs a combination of full database backups weekly, differential database backups hourly, and transaction log backups every five to ten minutes. These backups help protect your business from data loss. Use point-in-time restore to restore a database to an earlier time. For more information, see:

- [Restore a database from a backup in Azure SQL Database](/azure/sql-database/sql-database-recovery-using-backups)

- [Overview of business continuity with Azure SQL Database](/azure/sql-database/sql-database-business-continuity)

### SQL Server on virtual machines

For SQL Server running on virtual machines, there are two options: traditional backups and log shipping. Traditional backups enable you to restore to a specific point in time, but the recovery process is slow. Restoring traditional backups requires starting with an initial full backup, and then applying any backups taken after that backup.

The second option is to configure a log shipping session to delay the restore of log backups, for example, by two hours. This approach provides a window to recover from errors made on the primary.

### Azure Cosmos DB

Azure Cosmos DB automatically makes backups at regular intervals. Backups are stored separately in another storage service. Those backups are globally replicated for resiliency against regional disasters. If you accidentally delete your database or collection, you can file a support ticket or call Azure support to restore the data from the last automatic backup. For more information, see [Online backup and on-demand data restore in Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore).

### Azure Database for MySQL, Azure Database for PostgreSQL

When you use Azure Database for MySQL or Azure Database for PostgreSQL, the database service automatically makes a backup of the service every five minutes. Using this automatic backup feature, you can restore the server and all its databases into a new server to an earlier point-in-time. For more information, see:

- [How to back up and restore a server in Azure Database for MySQL using the Azure portal](/azure/mysql/howto-restore-server-portal)

- [How to backup and restore a server in Azure Database for PostgreSQL using the Azure portal](/azure/postgresql/howto-restore-server-portal)

## Distribute data geographically

Azure services support geographically distributed data, such as Azure SQL Database and SQL Server on virtual machines.

### SQL Database

Azure SQL Database provides two types of recovery: geo-restore and active geo-replication.

#### Geo-restore

[Geo-restore](/azure/sql-database/sql-database-recovery-using-backups/#geo-restore) provides the default recovery option when the database is unavailable because of an incident in the region where your database is hosted. It's also available with Basic, Standard, and Premium databases.

Similar to point-in-time restore, geo-restore relies on database backups in geo-redundant Azure storage. It restores from the geo-replicated backup copy, and therefore is resilient to the storage outages in the primary region. For more information, see [Azure SQL Database disaster recovery guidance](/azure/azure-sql/database/disaster-recovery-guidance).

#### Active geo-replication

[Active geo-replication](/azure/sql-database/sql-database-geo-replication-overview) is available for all database tiers. It's designed for applications that have more aggressive recovery requirements than geo-restore can offer. Using active geo-replication, you can create up to four readable secondaries on servers in different regions. You can initiate failover to any of the secondaries.

Active geo-replication can be used to support the application upgrade or relocation scenarios and load balancing for read-only workloads. For more information, see [Configure active geo-replication and failover](/azure/sql-database/sql-database-geo-replication-portal).

For information on how to design and implement applications and applications upgrade without downtime, see [Designing globally available services using Azure SQL Database](/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery) and [Managing rolling upgrades of cloud applications by using SQL Database active geo-replication](/azure/sql-database/sql-database-manage-application-rolling-upgrade).

### SQL Server on Azure Virtual Machines

Various options are available for recovery and high availability for SQL Server 2012 and later that run in Azure Virtual Machines. For more information, see [Business continuity and HADR for SQL Server on Azure Virtual Machines](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr).

### SQL Server Always On availability groups across regions

You can use SQL Always On availability groups for high availability by creating a single availability group that includes the SQL Server instances in both regions.

As an example, the [Multi-region N-tier application](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server) reference architecture shows practices for running an N-tier application in multiple Azure regions to achieve availability and a robust disaster recovery infrastructure. It uses a SQL Server Always On availability group and Azure Traffic Manager.

## Storage resiliency

Azure Storage provides data resiliency through automated replicas. However, this ability doesn't prevent application code or users from corrupting data, whether accidentally or maliciously. Maintaining data fidelity in the face of application or user error requires more advanced techniques, such as copying the data to a secondary storage location with an audit log.

- *Block blobs*. Create a point-in-time snapshot of each block blob. For more information, see [Create a snapshot of a blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob).

  For each snapshot, you're only charged for the storage required to store the differences within the blob since the last snapshot state. The snapshots are dependent on the existence of the original blob that they're based on. We recommend a copy operation to another blob or even another storage account. This approach ensures that backup data is properly protected against accidental deletion.

  You can use [AzCopy](/azure/storage/common/storage-use-azcopy) or [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) to copy the blobs to another storage account.

- *Files*. Use [share snapshots](/azure/storage/files/storage-snapshots-files), or use AzCopy or PowerShell, to copy your files to another storage account.

- *Tables*. Use AzCopy to export the table data into another storage account in another region.

:::image type="icon" source="../_images/github.png" border="false"::: For samples related to storage resiliency, see [Storage resiliency code samples](https://github.com/mspnp/samples/tree/master/Reliability/StorageSnapshotsSample). The scripts demonstrate these tasks:

- Deploy storage accounts, blob containers, and a file share with an Azure Resource Manager template.
- Copy a local file into a blob container.
- Create a blob snapshot.
- Copy a local file into a file share.
- Create a share snapshot.
- Use AzCopy to copy a blob container from one storage account to another.
