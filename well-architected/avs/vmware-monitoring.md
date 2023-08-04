---
title: Monitoring considerations for Azure VMware Solution
description: See best practices for monitoring Azure VMware Solution workloads at the infrastructure and application levels.
author: Mahesh-MSFT
ms.author: maksh
ms.date: 08/10/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Monitoring considerations for Azure VMware Solution

This article discusses the monitoring design area of an Azure VMware Solution workload. This area focuses on observability best practices. The guidance is intended for an operations team. Microsoft, VMware, and third parties provide various tools that you can use to monitor your infrastructure and application. This article lists those options.

Each option offers monitoring solutions with varying degrees of licensing costs, integration options, monitoring scope, and support. Carefully review the applicable terms and conditions before using the tools.

## Collect infrastructure data

*Impact: Operational excellence*

Monitoring your workload involves collecting data from Azure VMware Solution infrastructure and various VMware solution components. Azure VMware Solution is integrated with the VMware software-defined datacenter (SDDC), which runs several VMware solution native components such as VMware Aria. You can use this suite of tools, including VMware Aria Operations, to manage various aspects of your infrastructure.  

Another tool at your disposal is VMware vSphere Health Status for Azure VMware Solution. This tool helps ensure that proactive issue detection and remediation are continually performed in your Azure VMware Solution environment. In particular, this tool finds misconfigurations in the vSphere infrastructure and detects performance bottlenecks. It also provides insight into resource utilization and overall environmental health performance.

VMware Aria Operations for Networks helps you achieve comprehensive network visibility, streamline troubleshooting processes, and optimize network performance.

##### Recommendations

- Configure [vSphere Health Status](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.monitoring.doc/GUID-F957C1BB-A032-4648-9310-68A94733ABC8.html) to get a high-level view of the state of the Azure VMware Solution SDDC health.
- Use [VMware Aria Operations for Networks](https://www.vmware.com/products/aria-operations-for-networks.html) for enhanced visibility and analytics of Azure VMware Solution private cloud network infrastructure.

## Manage logs and archives

*Impact: Operational excellence*

To get health data from VMware solution components, you need access to logs that the VMware syslog service collects. Examples of solution components include Elastic Sky X integrated (ESXi), the VMware virtual storage area network (vSAN), NSX-T Data Center, and vCenter Server. Logs from these components are available through Azure VMware Solution infrastructure. A Log Analytics agent or extension sends guest logs at the virtual machine (VM) level to Log Analytics. Within Azure VMware Solution, you can send the Azure VMware Solution logs to an Azure native storage blob. To send logs to a storage blob, you can set up forwarders from a centralized syslog server, or you can configure the blob as a destination in Azure Monitor. It's also possible to use an Azure native tool such as Azure Logic Apps or Azure Functions to forward logs. You can use these tools to create listeners for incoming logs from Azure VMware Solution and to send the logs to a storage blob.

Archiving logs is a strategy for keeping your storage costs down. Azure Storage blobs and Log Analytics can transfer logs for long-term archival. Using a storage blob is the less expensive option. But Log Analytics has advanced integrations for alerting, visualization, querying, and gaining machine learning–based insights. Consider your budget, functional use cases, and long-term use cases when you choose a solution.

##### Recommendations

- Collect logs from the VMware syslog service to get health data from VMware solution components such as ESXi, the vSAN, NSX-T Data Center, and vCenter Server.
- Configure tools such as [Log Analytics](/azure/azure-vmware/configure-vmware-syslogs) to collect various logs for querying, analyzing, and reporting capabilities.
- Configure retention durations for sending logs to long-term storage to reduce query time and save on storage costs.
  
## Monitor the guest operating system

*Impact: Operational excellence*

Within the guest operating system, metrics are available for disk usage, application performance, system resource utilization, and user activity. Consider using Azure Arc for Azure VMware Solution (preview) to manage VMware infrastructure resources in Azure. For more information, see [Deploy Azure Arc for Azure VMware Solution](/azure/azure-vmware/deploy-arc-for-azure-vmware-solution).

##### Recommendations

- Enable guest management and install Azure extensions after your private cloud is enabled by Azure Arc.
- Install extra agents to collect data to enable guest management and monitoring on Azure VMware Solution guest VMs.  

## Implement security monitoring

*Impact: Security, Operational excellence*

Security monitoring is critical for detecting and responding to anomalous activities. Workloads that run in an Azure VMware Solution private cloud need comprehensive security monitoring that spans networks, Azure resources, and the Azure VMware Solution private cloud itself. You can centralize security events by deploying a Microsoft Sentinel workspace. By using this integration, the operation team can view, analyze, and detect security incidents in the context of a broader organizational threat landscape.

##### Recommendations

- Enable [Microsoft Defender for Cloud](/azure/defender-for-cloud/connect-azure-subscription) on the Azure subscription that you use to deploy the Azure VMware Solution SDDC. Ensure that in the Defender for Cloud plan, the **Cloud Workload Protection (CWP)** setting has a value of **ON** for servers.
- Audit actions that privileged users take on the Azure VMware Solution SDDC. For more information, see [Audit activity history for group assignments in privileged identity management](/azure/active-directory/privileged-identity-management/groups-audit).
- Integrate Microsoft Sentinel with Defender for Cloud. Enable its data collector for security events and connect it with Defender for Cloud.
- Use security monitoring solutions from validated partners in Azure VMware Solution.

## Monitor and analyze networks

*Impact: Security, Operational excellence*

The process of network monitoring inspects all the traffic that comes into and goes out of the Azure VMware Solution private cloud. In Azure VMware Solution, network security operates at the network and host layers.

##### Recommendations

- Capture and monitor network firewall logs that are deployed in the Azure VMware Solution private cloud. Also monitor logs that are deployed in Azure when your application extends to Azure native devices such as Azure Firewall or Azure Application Gateway. For more information, see [Firewall integration in Azure VMware Solution](https://techcommunity.microsoft.com/t5/azure-migration-and/firewall-integration-in-azure-vmware-solution/ba-p/2254961).
- Use [Azure Firewall Workbook](/azure/firewall/firewall-workbook) or similar tools to monitor common metrics and logs that are related to firewall devices.
- Correlate logs from multiple security vectors such as identity, networking, and infrastructure vectors.

## Configure and streamline alerts

*Impact: Operational excellence, Cost optimization*

When you run workloads in the Azure VMware Solution private cloud, you need to effectively monitor workload performance. For example, you should capture logs, metrics, and trace requests for your application and infrastructure layers.

Alerts can help you respond to changes in your performance baseline. You can also use alerts to provide information about necessary maintenance or configuration changes. For instance, you can receive notifications when a key expires, a connection is lost, or there's a risk of exceeding a resource's capacity.

To make alerts effective, configure them to notify accountable teams when certain conditions are met. Also consider consolidating alerts to reduce the number of individual notifications that are sent:

- Instead of issuing an alert for every machine that's low on space, consider consolidating alerts by hosts, resource groups, or clusters.
- Use this approach also with host issues, CPU, and storage spikes.
- Base alerts on time windows. For example, if a host issues alerts for a short time, you can suppress the alerts according to a defined time threshold. For instance, you can send an alert only after five minutes have passed.

##### Recommendations

- Discuss and establish baselines that are based on performance data.
- Define relevant alert criteria such as thresholds, severity levels, or specific conditions.
- Use the [vSphere events and alarms subsystem](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.monitoring.doc/GUID-9272E3B2-6A7F-427B-994C-B15FF8CADC25.html) to monitor vSphere and set up triggers.
- Configure [Azure alerts in Azure VMware Solution](/azure/azure-vmware/configure-alerts-for-azure-vmware-solution#supported-metrics-and-activities) to respond to events in real time.
- Ensure that alerts are configured so that [vSAN storage slack space](/azure/azure-vmware/concepts-storage) is maintained at the levels that your service-level agreement (SLA) mandates.
- Configure resource health alerts to get the real-time health status of the Azure VMware Solution private cloud.
- Use application performance monitoring (APM) tools to gain performance insights at the application code level.
- Use a combination of monitoring techniques such as synthetic transactions, heartbeat monitoring, and endpoint monitoring.
- Prioritize alerts based on their impact on operations or the criticality of the affected systems. Fine-tune alerts to trigger only meaningful events.
- To reduce noise and effectively manage alerts, use methods for reducing the number of individual notifications that are issued.
- To minimize alert fatigue, employ a mechanism to notify key stakeholders only about significant events.
- Use notification channels such as SMS, email, push notifications, and collaboration platforms such as Microsoft Teams to ensure that alerts are delivered effectively.

## Manage costs

*Impact: Cost optimization, Operational excellence*

Cost monitoring refers to the ability to keep track of costs that are associated with the Azure VMware Solution private cloud.

##### Recommendations

- Use the vSphere events and alarms subsystem to monitor vSphere and set up triggers.
- Configure Azure alerts in Azure VMware Solution. These alerts help your operation team respond to expected and unexpected events in real time.

## Use troubleshooting and debugging tools

*Impact: Cost optimization, Operational excellence*

To efficiently debug and troubleshoot your application, you need logs, metrics, and associated information. This information includes event activities so that you can identify, analyze, and establish connections between events.

##### Recommendations

- Configure your system to forward logs from the Azure VMware Solution syslog service to Log Analytics. Forward all relevant logs, metrics, and diagnostic information.
- Configure a server agent that's enabled by Azure Arc on guest VMs that run inside the Azure VMware Solution private cloud.

## Use dashboards

*Impact: Operational excellence*

Application dashboards help you visualize and monitor an application's performance, health, and other metrics:

- Monitoring reports in dashboards help you do root-cause analysis and troubleshooting quickly. Operations teams can use these dashboards to view all key resources that make up Azure VMware Solution in a single pane.
- Dashboard metrics provide insight into how code and infrastructure changes impact application behavior.
- Visuals help customer support teams understand the impact of changes, performance, and availability issues on an application.
- Performance indicators benefit executive leadership and business stakeholders. These tools inform decisions that align an application's performance with business objectives. For example, an executive can monitor commitment to customers by reviewing metrics such as service availability, incident resolution times, and average response times. These metrics help ensure that the organization delivers services according to its SLA.

Besides offering insight, dashboards can also promote transparency and encourage collaboration, for instance, when you grant appropriate stakeholders access to application dashboards. This act cultivates a shared comprehension of an application's performance. This practice also enables an organization to make informed decisions. As a result, stakeholders can focus on pursuing crucial initiatives that propel the business forward.

##### Recommendations

- Build an application dashboard with Application Insights or Grafana. Connect the dashboard to relevant data sources that store metrics from your Azure VMware Solution environment.
- Create an Azure workbook as a central repository for commonly run queries, metrics, and interactive reports.
- Ensure that your data source aligns with security and compliance requirements.
- Define access controls and permissions such as user authentication and role-based access control. Ensure that each stakeholder has appropriate access that's based on their role.
- Conduct periodic access reviews to check that user access is up to date and aligned with current roles and responsibilities.

## Next steps

Now that you've looked at observability best practices in Azure VMware Solution, explore mechanisms, tools, and perimeters that you can use to further secure workloads in the SDDC.

> [!div class="nextstepaction"]
> [Security](./vmware-security.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./vmware-assessment.md)