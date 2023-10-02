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

# Decouple from your Exadata

Exadata is an engineered system. It consists of both Hardware and Software. By decoupling we mean to decouple from your hardware into a single instance Database.

The Exadata has some unique features that includes storage indexes, flash cache, hybrid columnar compression and smart scan. 
At first we will need to review your AWR report and see how you leverage the Exadata features. In some cases, you might have even disabled it. 
Based on the data from the AWR we can provide you with the best solution fitting your workload and utilization. 
To become more comfortable to run your workload without the Exadata features, you can test it out. 

## How to size out an Exadata workload on Azure

The AWR (Automatic Workload Repository) is the starting point of any sizing exercise and also applies to Exadata Workloads. 

The AWR reports should be delivered in html format and on peak-load. In most cases Exadata AWR reports consists of several Databases hosted on an Exadata. The AWR report therefore should be handed over from the Database with the heaviest load. If you are unsure about the heaviest one, within the AWR report, scroll down to the “Top 10 Databases”. 

The interval of an AWR report should either be a 3-4 day interval or rather 1-hour peak-load. If you will need assistance to get to know your peak-times, please consult a Oracle SME in Microsoft to support you.

Next to it, we will need data covering the DB size, archive redologs, online logs and volume of backup. If you might need help, please feel free to consult an Oracle SME. 

After you collected all data and ran the right-size exercise, we will need to have a deeper look into the AWR report. Do you use hybrid columnar compression (HCC), Smart and/or Storage indexes? If the answer is yes to one of these, an additional x2 IO factor is expected. By digging a bit deeper into it, we will have a look if you make use of flash cache. If so, another x2 will be added to the right-sizing. 

As an important note it is always important how the Exadata features are leveraged. Sometimes you might have enabled it, but it impacts the performance significantly, which is mostly seen in OLTP workloads. 
You can review on how you leverage it in the “Top Time Foreground Wait Events”. For further read, please navigate to [Decoupling from Exadata](https://learn.microsoft.com/en-us/azure/azure-netapp-files/performance-oracle-multiple-volumes#decoupling-from-exadata).

## Most important Exadata Features to Review

There are a few parameters that are dependent on the Exadata performance. The following two are the most important to review:

_KCFIS_STORAGEIDX_DISABLED
CELL_OFFLOAD_PROCESSING

If you disable the storage indexes and the smart scan is enabled, everything is processed by smartscan without filtering. 
Both are very dependent on each other and worth to review when you want to see a non-Exadata behavior in order to gain confidence by decoupling from your Exadata hardware. 

### Test Database Performance Without Storage Indexes

The storage index defines the memory structures that reduce the amount of physical IO required on a cell node by tracking minimum and maximum values from a query predicate and builds the storage index based on the usage.

Summarizing to _KCFIS_STORAGEIDX_DISABLED is one of the parameters that controls the storage index functionality. It basically tells if the Oracle storage needs to be used while performing a query. 
By default, it is set to False which means that the storage index enabled. 
In order to test how your database performs without the Exadata features you can use the following query:

SQL>alter system set _KCFIS_STORAGEIDX_DISABLED=TRUE;

**Note: if you want to test it only on session level, please replace >system< through >session>**

### Test Database Performance Without Cell Offloading

CELL_OFFLOAD_PROCESSING is the smart scan function within the Exadata. It turns the offloading on and off. By default, it is set to true which means that smart scan is enabled. 
To disable Smart Scan you need to run the following command: 

SQL>alter system set CELL_OFFLOAD_PROCESSING=FALSE;

**Note: if you want to test it only on session level, please replace >system< through >session>**

If you disable this functionality the Databases will behave like non-Exadata and do its own disk I/O through the buffer cache or direct read.

### Test Database with loss of Flash Cache

Commonly the flash cache performance is tested on 1-10 tables that could be impactful.  You can test it out before bringing your database on Azure to review the difference when access to Flash Cache in Exadata is removed.
You can run the following command: 

ALTER TABLE ….STORAGE(CELL_FLASH_CACHE NONE);

This automatically generates a script for a larger set of objects. Run this command in on and off mode. If you have done so, compare the optimized IO to verify the performance impact.

### Test Database Performance without Hybrid Columnar Compression

Before you make any test, make sure to review your current settings first. 

If you want to review where you enabled this feature, you can run the following command: 

SQL> SELECT table_name, compress_for 
FROM  dba_tables 
WHERE COMPRESSION = ‘ENABLED’;

In My Oracle Support (Doc ID: 1080301.1) you can review how to disable Hybrid Columnar Compression. 

## Plan to Migrate your Exadata Workload

Review your current architecture:
-	Application /Web & App Servers
-	ETL processes
-	Middleware
-	Jump Server 
-	Monitoring Systems
-	Load Balancer
-	Any connected other systems
Every content and dependencies should be tracked in a migration plan. Have one responsible project lead and one responsible person as backup in case of vacation or any other reasons to not start from scratch. Ensure all technical resources are dedicated and assigned to the migration project.
When preparing your migration to a cloud journey, please determine your specific High Availability and Disaster Recovery architecture described in the Reliability Section.
For your application, make sure to review Azure Site Recovery.

## Next Steps

In case you will need to familiarize yourself with the capacity planning, please review [Compute and Storage][.\compute-and-storage-md]


