---
title: Monitor your Oracle workload
description: Review the design area for monitoring of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: jhaessler
ms.date: 04/22/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-oracle
---

# Monitor your Oracle workload

It's important to monitor your Oracle workloads on Azure virtual machines (VMs) to discover failures and abnormalities and ensure the health of your mission-critical workloads. This involves not only monitoring the database itself by using native Oracle tools, but also monitoring the infrastructure components used in your deployment.

## Monitor an Oracle database by using native Oracle tools

[Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager) is an integrated enterprise-management product that provides features to monitor events, incidents, and metrics against target Oracle workloads. You can use Oracle Enterprise Manager on a separate VM to monitor, maintain, and manage Oracle environments. The product provides clearly formatted text logs that you can export and load into Azure Monitor Logs to offer a single view when monitoring Oracle database information. You can also use management packs with Oracle to add more functionality, including the ability to "simulate" a platform as a service (PaaS) experience for infrastructure as a service (IaaS) solutions like Oracle on Azure.

Another native Oracle capability is the Automatic Workload Repository (AWR), which provides monitoring features to collect, process, and maintain performance statistics for problem detection and self-tuning. This monitoring helps you understand historical analytics and identify problems affecting database performance. In environments where AWR isn't running (for example, Oracle Standard Edition), you can use Statspack reports that include a summary and details of database instance statistics, wait events, and system statistics.

### Recommendations

Follow these recommendations when using Oracle Enterprise Manager monitoring features to monitor your Oracle database:

- Integrate Oracle Enterprise Manager text logs with Azure Monitor Logs to have a single view for monitoring.
- Continuously review your AWR or Statspack reports.

## Monitor VMs and storage

When you deploy your Oracle database on an Azure VM, it's important to monitor not only the database, but also the health telemetry from your storage and VM. Monitor provides many ways to monitor metrics and logs from the VMs and storage components. Azure [VM insights](/azure/azure-monitor/vm/vminsights-enable-portal) offers an easy way to monitor your client workloads by displaying an inventory of your existing VMs and guiding you through implementing base monitoring for the workloads. The VM insights feature also monitors VM performance and health by collecting data on running processes and VM dependencies on other resources.

### Recommendations

Follow these recommendations when monitoring your VMs and storage:

- Enable VM insights.
- If you need more information than VM insights provides, you can deploy the Azure Monitor Agent (AMA) and configure a data collection rule (DCR) to collect [custom logs](/azure/azure-monitor/agents/data-collection-text-log).
- Monitor disk metrics if you store Oracle database files on Azure managed disks. Disk metrics you can monitor include:
  - OS Disk IOPS Consumed Percentage
  - Data Disk IOPS Consumed Percentage
  - Data Disk Read Bytes/Sec
  - Data Disk Write Bytes/Sec
  - Disk Queue Depth
- If you store database files in Azure NetApp Files volumes, you should monitor metrics for allocated storage, actual storage usage, volume input/output operations per second (IOPS), throughput, and latency.
- Create Azure Monitor alerts to notify you of issues like thresholds being exceeded.
- In addition to monitoring VMs and storage, monitor other components within your solutions like Azure Virtual Network and the Azure Backup service.

## Next steps

Depending on your business case, refer to the [design principles](review-design-principles.md) for more information.
