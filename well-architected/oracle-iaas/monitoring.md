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

# Monitor your Oracle Workload
Monitoring and diagnostics are crucial for Oracle on Azure IaaS. As Oracle is a non-native solution running in Azure, Oracle Cloud Control offers the ability to monitor, manage and automate much of Oracle with little additional cost. Oracle Cloud Control comes with a Limited Use Enterprise Edition License, so the database repository does NOT require additional licensing, only the management packs are licensed to each target database. This ensures the same or similar tools that Oracle technologists have used on-premises are available in the cloud. We recommend you review the [Operational excellence design principles](https://learn.microsoft.com/en-us/azure/well-architected/devops/principles).

Oracle Enterprise Manager has text logs with a clear format that can be exported and loaded into Azure Log Analytics to offer a single pane of glass when monitoring Oracle database information. Oracle Enterprise Manager can be used with management packs to add more functionality, including “simulating” a PaaS experience for IaaS solutions such as Oracle in Azure. When using the Cloud and Lifecycle Management Packs for Oracle Cloud Control, full cycle management can be added to the system- automating patching of both database and OS, along with cloning and deployments.

We also encourage you to continously review your AWR and/or statspack report(s).

## Monitor VM and Storage
As you are deploying your Oracle Database on a Virtual Machine it is important, to not only use the Enterprise Manager to monitor the Oracle Database, but also the health telemetrics from your storage and VM. 

Therefore it is recommended to make use of log monitoring from Azure Monitor.
You should enable BM insights and observe the metrics of your managed disks. Additionally, you should keep having an overview about the following metrics:
- OS Disk IOPS Consumed Percentage  
- Data Disk IOPS Consumed Percentage 
- Data Disk Read Bytes/Sec  
- Data Disk Write Bytes/Sec  
- Disk Queue Depth

In case you have Azure Netapp Files in use as well, it is recommended to monitor this storage solution.

We strongly advice to enable and configure alerts to be able te react quickly.

## Azure policy and operational excellence
Many built-in Azure Policy definitions come in Deploy if Not Exists (DINE) versions for the resources that commonly make up Oracle solutions on Azure. Infrastructure as code (IaC) is the gold standard for resource deployment in business-critical applications, such as Oracle workloads. DINE policies perform imperative operations on your infrastructure and happen outside of the declarative model of IaC. Consider their usage carefully, opting for IaC-based deployments of infrastructure and architecture decisions, leaving Azure Policy for governance, not workload deployment/configuration.

## Next Steps
Depending on your business case please refer to according Design Area.
