---
title: Decouple workloads from Oracle Exadata
description: Review the design area for exadata-decouple of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: jhaessler
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-oracle
---

# Decouple workloads from Oracle Exadata

Oracle Exadata is an engineered system that has both hardware and software. This article describes how to decouple workloads from your Exadata hardware into a single Oracle Database instance.

Follow these steps to decouple your workloads from Exadata:

1. Review your automatic workload repository (AWR) report, and determine how you use Exadata features. Sometimes, you might even have disabled features. Exadata features include storage indexes, flash cache, hybrid columnar compression, and smart scan.
1. Based on the data from the AWR, choose the best solution for your workload and usage.
1. Test your workload to familiarize yourself with how it runs without Exadata features.
1. Migrate your Exadata workload.

## Size out an Exadata workload on Azure

The AWR is the starting point of any sizing exercise and also applies to Exadata workloads.

For your AWR reports, use data from your peak load, and create the reports in HTML format. In most cases, Exadata AWR reports consist of several databases that are hosted on Exadata. Use the database with the heaviest load to determine your solution. Check the *top ten databases* in the AWR report to find the database with the heaviest load.

Perform one-hour peak-load AWR reports every three to four days. Collect data about the database size, archive redo logs, online logs, and volume of the backup. For assistance, you can [connect with a specialist](https://www.oracle.com/cloud/azure/oracle-database-at-azure/).

After you collect all data and run the right-size exercise, take a deeper look into the AWR report. Determine whether you use the hybrid columnar compression feature, smart indexes, or storage indexes. If you do, expect twice as much input/output (I/O). If you use flash cache, double your I/O again.

It's important to note how you use Exadata features. Features can significantly affect performance, especially in online transactional processing (OLTP) workloads. To determine how features affect your performance, check the *top ten foreground wait events* in the AWR report. For more information, see [Decoupling from Exadata](/azure/azure-netapp-files/performance-oracle-multiple-volumes#decoupling-from-exadata).

## Most important Exadata Features to Review

There are a few parameters that are dependent on Exadata performance. The following two are the most important to review:

`_KCFIS_STORAGEIDX_DISABLED`  
`CELL_OFFLOAD_PROCESSING`

If you disable the storage indexes and enable the smart scan feature, the smart scan feature processes all data. No data is filtered. Both features are dependent on each other. and worth to review when you want to see a non-Exadata behavior in order. to gain confidence by decoupling from your Exadata hardware. 

### Test database performance without storage indexes

A storage index defines memory structures that reduce the amount of physical I/O that's required on a cell node. The storage index tracks the minimum and maximum values from a query predicate and builds the storage index based on the usage.

The `_KCFIS_STORAGEIDX_DISABLED` parameter controls the storage index functionality. This parameter determines whether Oracle storage needs to be used while performing a query. By default, it's set to `false`, which means that the storage index is enabled. To test how your database performs without the Exadata features, use the following query:

`SQL>alter system set _KCFIS_STORAGEIDX_DISABLED=TRUE;`

> [!NOTE]
> If you want to test only on a session level, replace `system` with `session`.

### Test database performance without cell offloading

The `CELL_OFFLOAD_PROCESSING` parameter manages the smart scan function within Exadata. This parameter turns offloading on and off. By default, it's set to `true`, which means that smart scan is enabled. To disable the smart scan feature, run the following command: 

`SQL>alter system set CELL_OFFLOAD_PROCESSING=FALSE;`

> [!NOTE]
> If you want to test only on a session level, replace `system` with `session`.

If you disable this functionality, the databases behave like non-Exadata databases and have their own disk I/O via a buffer cache or direct read.

### Test database without flash cache

Flash cache performance is typically tested on one to ten tables that are potentially impactful. Test flash cache performance before you migrate your database to Azure, so you can compare the difference with and without the flash cache feature.
You can run the following command: 

`ALTER TABLE <table name> STORAGE(CELL_FLASH_CACHE NONE);`

This automatically generates a script for a larger set of objects. Run this command in on and off mode. If you have done so, compare the optimized IO to verify the performance impact.

### Test database performance without hybrid columnar compression

Before you make any test, make sure to review your current settings first. 

If you want to review where you enabled this feature, you can run the following command: 

SQL> SELECT table_name, compress_for 
FROM  dba_tables 
WHERE COMPRESSION = ‘ENABLED’;

In My Oracle Support (Doc ID: 1080301.1) you can review how to disable Hybrid Columnar Compression. 

## Migrate your Exadata workload

Review your current architecture:

-	Application, jump, and web servers
-	ETL processes
-	Middleware
-	Monitoring systems
-	Load balancers
-	Other systems that are connected

Every content and dependencies should be tracked in a migration plan. Have one responsible project lead and one responsible person as backup in case of vacation or any other reasons to not start from scratch. Ensure all technical resources are dedicated and assigned to the migration project.
When preparing your migration to a cloud journey, please determine your specific High Availability and Disaster Recovery architecture described in the Reliability Section.
For your application, make sure to review Azure Site Recovery.

## Next step

To familiarize yourself with capacity planning, see [Compute and storage](choose-compute-storage.md).


