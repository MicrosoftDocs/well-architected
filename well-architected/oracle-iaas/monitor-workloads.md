---
title: Oracle on Azure IaaS design area compute and storage
description: Review the design area for monitoring of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: 
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-Oracle
---

# Monitor your Oracle Workload
Monitoring Oracle workloads on Azure Virtual Machines to discover failures and abnormalities is important to ensuring the health of your mission critical workloads. This involves not only monitoring the database itself with Oracle native tools but also monitoring the infrastructure components used in the deployment.

## Monitor the Oracle database with native Oracle tools
[Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager) is an Oracle integrated enterprise management product. It provides the monitoring features of events, incidents and metrics against the target Oracle workloads. Oracle Enterprise Manager can be deployed on a separate Virtual Machine to monitor, maintain, and manage Oracle environments. It has text logs with a clear format that can be exported and loaded into Azure Log Analytics to offer a single pane of glass when monitoring Oracle database information. Oracle Enterprise Manager can be used with management packs to add more functionality, including “simulating” a PaaS experience for IaaS solutions such as Oracle in Azure. 

Another native capability is the AWR (Automatic Workload Repository) which provides the monitoring features to collect, process, and maintain performance statistics for the purpose of problem detection and self-tuning. This monitoring helps you to realize historical analytics and identify the problems affecting the performance of the database. In environments where AWR is not running, e.g. in Oracle Standard Edition. Statspack reports can be used as they include the summary and details of database instance statistics, wait events, system statistics, etc.

### Assessment question
What Oracle monitoring tools do you use to monitor the database operation?

### Recommendations
- Make use of the Oracle Enterprise Manager monitoring features to monitor your oracle database.
- Integrate text logs from Oracle Enterprise Manager with Azure Log Analytics to have a single pane of glass for monitoring.
- Continuously review your AWR and/or Statspack report(s).

## Monitor VMs and Storage
As you are deploying your Oracle Database on Azure Virtual Machines it is important, to not only monitor the Oracle Database, but also the health telemetry from your storage and VM. Azure Monitor provides numerous ways to monitor metrics and logs from the Virtual Machines and storage components. Azure [VM Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-enable-portal) provides a quick and easy method for getting started monitoring the client workloads on your virtual machines. It displays an inventory of your existing VMs and provides a guided experience to enable base monitoring for them. It also monitors the performance and health of your virtual machines by collecting data on their running processes and dependencies on other resources.

### Assessment question
What tools do you use to monitor the virtual machines and storage components of your Oracle on IaaS workload?

### Recommendations
- Enable Virtual Machine insights.
- If you need more information than that provided by VM Insights then you can deploy the Azure Monitoring Agent (AMA) and configure a Data Collection Rule (DCR) to collect [custom logs](https://learn.microsoft.com/en-us/azure/azure-monitor/agents/data-collection-text-log?tabs=portal).
- Monitor Disks Metrics if you are storing Oracle database files on Azure Managed Disks.
- Disk Metrics to be monitored include:
    - OS Disk IOPS Consumed Percentage
    - Data Disk IOPS Consumed Percentage
    - Data Disk Read Bytes/Sec
    - Data Disk Write Bytes/Sec
    - Disk Queue Depth
- If the database files are stored in Azure NetApp Files (ANF) volumes, you should monitor ANF metrics for allocated storage, actual storage usage, volume IOPS, throughput and latency.
- Create Azure Monitor Alerts to notify of any issue such as thresholds being exceeded.
- In additions to VMs and Storage you should also monitor other components within your solutions such as Azure virtual network and Azure backup.

## Next Steps
Depending on your business case please refer to [Design principles](reivew-design-principles.md).
