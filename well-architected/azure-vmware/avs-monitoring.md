---
title: Monitoring considerations for Azure VMware Solution (AVS)
description: Best practices for monitoring VMWare workloads at the infrastructure and application levels.
author: Mahesh-MSFT
ms.author: maksh
ms.date: 06/05/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Monitoring considerations for Azure VMware Solution (AVS)

**Impact**: _Operational Excellence_

This design area focuses on observability best practices for a VMware workload. The guidance is intended for the operations team. To monitor the infrastructure and the application, a combination of tools provided by Microsoft, VMware, and third-parties can be used. This article lists those options. 

Each option offers monitoring solutions with varying degree of licensing costs, integration options, monitoring scope and support. Carefully, review terms and conditions before using the tools.



## Collect infrastructure data

To monitor the workload, you'll need to collect data from various VMware components and Azure VMware Solution (AVS) infrastructure. 

- Azure VMware Solution (AVS) is integrated with VMware Software-Defined Data Center (SDDC) that runs several VMware native components. Configure [vSphere Health Status](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.monitoring.doc/GUID-F957C1BB-A032-4648-9310-68A94733ABC8.html) to get a high-level overview of **Azure VMware Solution SDDC's health status**. Use [vRealize Network Insight](https://docs.vmware.com/en/VMware-vRealize-Network-Insight/6.2/com.vmware.vrni.using.doc/GUID-3A09A9F1-23FE-44C5-A1F0-DB1932BB8E45.html) to get enhanced visibility and analytics of Azure VMware Solution SDDC's network infrastructure.

  A popular tool for monitoring infrastructure and application is [vRealize Operations for Azure VMware Solution](/azure/azure-vmware/vrealize-operations-for-azure-vmware-solution). 

- You'll need logs collected by **VMware Syslog** to get health data from the VMware system components, such as ESXi, vSAN, NSX-T, vCenter, and others. These logs are available through Azure VMware Solution (AVS) infrastructure. Configure [Azure Log Analytics](/azure/azure-vmware/configure-vmware-syslogs) to collect those logs for querying, analyzing and reporting purposes.

     There might be tradeoffs on cost. Configure [cost control](/azure/well-architected/services/monitoring/log-analytics/cost-optimization#configuration-recommendations) for storing and managing costs associated with Azure Monitor.

- To enable **guest management and monitoring** on AVS guest VMs, you'll need to install additional agents to collect data. Use [Azure Arc for Azure VMware Solution (Preview)](/azure/azure-vmware/deploy-arc-for-azure-vmware-solution). For example, [Azure Log Analytics agent/extension](/azure/azure-vmware/integrate-azure-native-services) sends guest VM level logs to Azure Log Analytics. 

- Use native monitoring tools and processes for **database activities**. For example, if SQL server is used, [SQL Server assessment](/azure/azure-sql/virtual-machines/windows/sql-assessment-for-sql-vm) is recommended to support database monitoring on Azure VMware Solution SDDC.

There are third-party solutions available. Use [Partner solutions for application performance monitoring](/azure/azure-vmware/ecosystem-app-monitoring-solutions#third-party-solutions). Such solutions enable operation teams to continue using tool of their choice.
  
## Security monitoring

- Use [security monitoring solutions from Partners](/azure/azure-vmware/ecosystem-security-solutions). These tools are pre-validated to be used for Azure VMware Solution SDDC.

- Enable [Azure Defender for Cloud](/azure/defender-for-cloud/get-started) on the Azure subscription used for deploying Azure VMware Solution SDDC. Ensure that the [defender plan](https://azure.microsoft.com/pricing/details/defender-for-cloud/) has "Cloud Workload Protection (CWP)" ON for Servers. Operations team will be able to cover Azure VMware Solution guest VMs with threat protection.
  
- For network security, capture and monitor [Network Firewall](https://techcommunity.microsoft.com/t5/azure-migration-and/firewall-integration-in-azure-vmware-solution/ba-p/2254961) logs deployed either in Azure VMware Solution SDDC or Azure. Run guest VMs behind this firewall.

    Use [Azure Firewall Workbook](/azure/firewall/firewall-workbook) or similar tools to monitor common metrics and logs related to firewall device.

- Audit [activities by privileged users](/azure/active-directory/privileged-identity-management/groups-audit) on Azure VMware Solution SDDC.

- You can centralize security events by deploying [Microsoft Sentinel workspace](/azure/azure-vmware/azure-security-integration#deploy-a-microsoft-sentinel-workspace). Enable it's data collector for security events and connect it with Microsoft Defender for Cloud. With this integration, the operation team can view, analyze, and detect security incidents in the context of broader organizational threat landscape. For more information, see these articles:

    - [Enable data collector](/azure/azure-vmware/azure-security-integration#enable-data-collector-for-security-events)
    - [Integrate Sentinel with Defender for Cloud](/azure/azure-vmware/azure-security-integration#connect-microsoft-sentinel-with-microsoft-defender-for-cloud)
 
### Alerts and notifications

You also need to configure alerts so that notifications are sent to the accountable teams when certain conditions are met.

- [vSphere events and alarms subsystem](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.monitoring.doc/GUID-9272E3B2-6A7F-427B-994C-B15FF8CADC25.html) is recommended for monitoring vSphere and setting up triggers.

- Configure [Azure Alerts in Azure VMware Solution](/azure/azure-vmware/configure-alerts-for-azure-vmware-solution#supported-metrics-and-activities). Such alerts enable operation teams to respond to expected and unexpected events in real-time.

- Understand [Azure VMware Solution Service Level Agreement (SLA) commitments](https://azure.microsoft.com/support/legal/sla/azure-vmware/v1_1/). Ensure that alerts are configured so that [vSAN storage slack space](/azure/azure-vmware/concepts-storage) is maintained at the levels mandated by SLA agreement.

- Configure [Resource Health alerts](/azure/service-health/resource-health-alert-monitor-guide). Operations teams can use these alerts to get real-time health status of Azure VMware Solution SDDC.

## Visualization

It's highly recommended that you visually represent the monitoring reports in dashboards to drive effective operations. This will help the operations team to quickly do root-cause analysis and troubleshooting. 

- Configure [Monitoring dashboard](https://github.com/Azure/Enterprise-Scale-for-AVS/tree/main/BrownField/Monitoring/AVS-Dashboard). Operation teams can use such dashboard for a simplified view of all key resources that make up Azure VMware Solution in a single pane.

- Create [Azure Workbook](/azure/azure-monitor/visualize/workbooks-overview) as a central repository for commonly executed queries, metrics and interactive reports.

