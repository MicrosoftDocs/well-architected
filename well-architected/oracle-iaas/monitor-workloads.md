---
title: Monitor your Oracle workload
description: Review the Azure Well-Architected Framework design area for monitoring. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: jhaessler
ms.date: 04/22/2024
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-workload-oracle
---

# Monitor your Oracle workload

This article describes how to use Azure Virtual Machines to monitor your Oracle workloads. You can identify failures and abnormalities to ensure the health of your mission-critical workloads. This approach involves not only monitoring the database itself by using native Oracle tools, but also monitoring the infrastructure components that you use in your deployment.

## Use native Oracle tools to monitor an Oracle database

[Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager) is an integrated enterprise-management platform that provides features to monitor events, incidents, and metrics against target Oracle workloads. You can use Oracle Enterprise Manager on a separate virtual machine (VM) to monitor, maintain, and manage Oracle environments. This platform provides clearly formatted text logs that you can export and load into Azure Monitor Logs to offer a single view when you monitor Oracle database information. You can also use management packs with Oracle to add more functionality, including the ability to "simulate" a platform as a service (PaaS) experience for infrastructure as a service (IaaS) solutions like Oracle on Azure.

Another native Oracle capability is the automatic workload repository (AWR), which provides monitoring features to collect, process, and maintain performance statistics for problem detection and self-tuning. Monitor your workload to understand historical analytics and identify problems that affect database performance. In environments where AWR isn't running, for example Oracle Standard Edition, you can use Statspack reports that include a summary and details of database instance statistics, wait events, and system statistics.

### Recommendations

Follow these recommendations when you use Oracle Enterprise Manager monitoring features to monitor your Oracle database:

- Integrate Oracle Enterprise Manager text logs with Azure Monitor Logs to have a single view for monitoring.
- Continuously review your AWR or Statspack reports.

## Monitor VMs and storage

When you deploy your Oracle database on an Azure VM, you must monitor not only the database, but also the health telemetry from your storage and VM. Azure Monitor provides many ways to monitor metrics and logs from the VMs and storage components. Azure [VM insights](/azure/azure-monitor/vm/vminsights-enable-portal) offers an easy way to monitor your client workloads by displaying an inventory of your existing VMs and guiding you through implementing base monitoring for the workloads. The VM insights feature also monitors VM performance and health by collecting data on running processes and VM dependencies on other resources.

### Recommendations

Follow these recommendations when you monitor your VMs and storage:

- Enable VM insights.
- If you need more information than VM insights provides, you can deploy the Azure Monitor Agent (AMA) and configure a data collection rule (DCR) to [collect custom logs](/azure/azure-monitor/agents/data-collection-text-log).
- Monitor disk metrics if you store Oracle database files on Azure managed disks. Disk metrics you can monitor include:
  - OS disk input/output operations per second (IOPS) consumed percentage
  - Data disk IOPS consumed percentage
  - Data disk read bytes/sec
  - Data disk write bytes/sec
  - Disk queue depth
- If you store database files in Azure NetApp Files volumes, you should monitor metrics for allocated storage, actual storage usage, volume IOPS, throughput, and latency.
- Create Monitor alerts to notify you of problems like exceeded thresholds.
- In addition to monitoring VMs and storage, monitor other components within your solutions like Azure Virtual Network and Azure Backup.

## Next step

> [!div class="nextstepaction"]
> [Design principles for an Oracle workload on Azure](review-design-principles.md)
