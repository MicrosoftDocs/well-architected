---
title: Monitoring considerations for Azure Virtual Desktop workloads
description: See best practices for monitoring the performance and status of your Azure Virtual Desktop workloads.
author: peegee1184
ms.author: jorodas
ms.date: 10/12/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Monitoring considerations for Azure Virtual Desktop workloads

This article discusses the monitoring design area of an Azure Virtual Desktop workload. Before you start using Azure Monitor for Azure Virtual Desktop, you need to take the following steps:

- Configure at least one Log Analytics workspace. Use a designated Log Analytics workspace for your Azure Virtual Desktop session hosts to help ensure that performance counters and events are collected only from session hosts in your Azure Virtual Desktop deployment.
- Enable data collection for the following items of your Log Analytics workspace:
  - Diagnostics from your Azure Virtual Desktop environment
  - Recommended performance counters from your Azure Virtual Desktop session hosts
  - Recommended Windows event logs from your Azure Virtual Desktop session hosts

The following sections provide considerations for monitoring your Azure Virtual Desktop environment and keeping it healthy.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-an-azure-virtual-desktop-workload).

## Health and availability monitoring  

*Impact: Operational Excellence, Reliability*

Azure offers several services such as Azure Service Health and Azure Resource Health that keep you informed about the health of your cloud resources.

### Service Health

You should use Service Health to provide a view of the health of the Azure services and regions that you use. Service Health is the best place to look for notifications about events that impact your service, such as outages and planned maintenance activities. Service Health also provides other health advisories about impacts to your Azure Virtual Desktop environment and related subscription. The most proactive approach is to set up Service Health alerts. You can receive these alerts via your preferred communication channels. The alerts notify you when service issues, planned maintenance, or other changes might affect your Azure Virtual Desktop resources. For more information, see [Set up service alerts](/azure/virtual-desktop/set-up-service-alerts).

### Resource Health

Resource Health helps you diagnose and get support for service problems that affect your Azure resources. Information about the current and past health of your resources is reported in Resource Health. Be sure to set up proactive alerting to notify you about any undesirable resource health statuses. You can monitor the following resource types for resource health status:

- Azure Storage solutions for Azure Virtual Desktop FSLogix and App Attach
- Session hosts, or virtual machines (VMs)

##### Recommendations

- Use Service Health to stay informed about the health of the Azure services and regions that you use.
- Set up Service Health alerts so that you stay aware of service issues, planned maintenance, or other changes that might affect your Azure Virtual Desktop resources.
- Use Resource Health to monitor your VMs and storage solutions.
- Set up Resource Health alerts.

## Performance monitoring

*Impact: Performance Efficiency, Operational Excellence*

To gain visibility and monitor performance, you need to monitor critical components of your Azure Virtual Desktop environment.

### Configuration recommendations

To help ensure that you retrieve key performance indicators and necessary logs from your Azure Virtual Desktop entities, configure the following diagnostics data to be sent to Log Analytics:

- Azure Virtual Desktop host pool logs
- Azure Virtual Desktop workspace diagnostics
- Azure Virtual Desktop application group diagnostics
- Storage diagnostics
- Data about session hosts from a Monitor agent or Log Analytics agent
- Performance and event log data that's collected according to Monitor or Log Analytics agent data collection rules
- Azure VM insights data

### Configuration options

Several options are available for configuring diagnostic settings:

- An Azure Virtual Desktop Insights configuration workbook. Azure Virtual Desktop Insights is a dashboard that's built on Monitor workbooks that helps you understand your Azure Virtual Desktop environment. Azure Virtual Desktop Insights provides a configuration workbook that you can use to:
  - Configure host pool and workspace diagnostics.
  - Enable monitoring agents on your session hosts.
  - Configure recommended performance counters and event logs for monitoring your Azure Virtual Desktop environment.

  You can collect other key performance indicators by configuring the agent settings of your Log Analytics workspace.

- Azure Policy. You can use Azure Policy to help ensure that monitoring agents are installed on your VMs. Azure Policy supports Monitor agents and Microsoft monitoring agents.

- Deployment configuration and templates. You can use the Azure portal or a declarative deployment solution such as Azure Resource Manager templates (ARM templates), BICEP, or Terraform to deploy Azure Virtual Desktop. Ensure that diagnostic settings are deployed as part of your Azure Virtual Desktop deployment configuration. If you deploy Azure Virtual Desktop via the Azure portal, ensure that diagnostic settings are enabled. For the declarative deployment models, ensure that host pools, workspaces, or application groups are deployed with diagnostic settings enabled. Also ensure that VMs are deployed with Microsoft monitoring agent or Monitor agent extensions.

### Session host performance

Performance counters record how your system resources are used. Performance counter data ingestion depends on your environment size and usage. It's important to understand that each performance counter sends data at a specific frequency. In your Azure Virtual Desktop Insights configuration workbook, you can adjust your per-minute default sample rate. You can also edit this rate in your settings. The multiplying factor that's applied to this rate depends on the counter.

The following list shows the categories of performance metrics and the performance counters that you can configure in each category:

- Logical disk
  - `Free Space`
  - `Avg. Disk Queue Length`
  - `Avg. Disk sec/Transfer`
  - `Current Disk Queue Length`
- Memory
  - `% Committed Bytes in Use`
  - `Available Mbytes`
  - `Page Faults/sec`
  - `Pages/sec`
- Physical disk
  - `Avg. Disk Queue Length`
  - `Avg. Disk sec/Read`
  - `Avg. Disk sec/Transfer`
  - `Avg. Disk sec/Write`
- Processor information
  - `% Processor Time`
  - `RemoteFX network`
  - `Current TCP RTT`
  - `Current UDP Bandwidth`
  - `Terminal Services`
  - `Active Sessions`
  - `Inactive Sessions`
  - `Total Sessions`
- User input delay per process
  - `Max Input Delay`
- User input delay per session
  - `Max Input Delay`

You can use diagnostics tables in Log Analytics to query and analyze network information for Azure Virtual Desktop connections. The `WVDConnectionNetworkData` data includes a correlation ID that maps to a specific Azure Virtual Desktop connection. For each session, you can see critical performance data such as the estimated round-trip time in milliseconds and the estimated available bandwidth in KBps.

Windows event logs are data sources that Log Analytics agents collect on Windows VMs. You can collect events from standard logs, such as system and application logs. You can also collect events from custom logs that are created by applications that you need to monitor. By default, logs from the following types of Windows events are collected for Azure Virtual Desktop in Monitor:

- `Application`
- `Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin`
- `Microsoft-Windows-TerminalServices-LocalSessionManager/Operational`
- `System`
- `Microsoft-FSLogix-Apps/Operational`
- `Microsoft-FSLogix-Apps/Admin`

A Windows event is triggered whenever the terms of the event are met in the environment. Machines in healthy states send fewer events than machines in unhealthy states.

It's important to monitor the event logs for connection problems that can come up with the Azure Virtual Desktop agent. For more information, see [Troubleshoot common Azure Virtual Desktop agent issues](/azure/virtual-desktop/troubleshoot-agent).

### Storage performance thresholds and monitoring

You should monitor the Azure storage solution that you use for hosting FSLogix profiles or App Attach shares. It's important to monitor the profile storage locations to help ensure that thresholds aren't exceeded, which might have a negative impact on your user experience. For Storage, you should monitor the file share capacity to help ensure that file share quotas don't reach maximum capacity. You should also monitor the file share transactions to help ensure that transactions are within defined thresholds.

### Service limits

Azure Virtual Desktop has service limits that you should consider during the design phase of your deployment. You need to be aware of these service limits also in production environments, and you should proactively report on these limits. The limits are relatively large. But in larger deployments, where it's easier to reach these limits, it's essential to monitor them. For more information, see [Azure Virtual Desktop limitations](/azure/architecture/example-scenario/wvd/windows-virtual-desktop#azure-virtual-desktop-limitations).

##### Recommendations

- Configure diagnostics data to be sent to Log Analytics.
- Select from various options for configuring diagnostics settings, such as an Azure Virtual Desktop Insights configuration workbook, Azure Policy, the Azure portal, or a template.
- Configure performance counters so that you have a record of how your system resources are used.
- Use diagnostics tables in Log Analytics to query and analyze network information for Azure Virtual Desktop connections.
- Monitor event logs for connection problems with the Azure Virtual Desktop agent.
- Monitor the Azure storage solution that you use for hosting FSLogix profiles or App Attach shares.
- Monitor service usage to make sure your workload doesn't exceed limits.

## Security monitoring

*Impact: Security*

The following sections describe several monitoring measures that you can take to improve security in your workload.

### Microsoft Defender for Cloud and Microsoft Sentinel  

Ensure that Microsoft Defender for Cloud enhanced security features are enabled on the subscription and Azure Virtual Desktop session hosts that you deploy. Defender for Cloud provides a cloud workload protection platform and Cloud Security Posture Management. You can use Defender for Cloud to manage vulnerabilities and to assess compliance with common frameworks like Payment Card Industry (PCI) and ISO27001. You can also use Defender for Cloud to strengthen the overall security posture of your environment. Defender for Cloud uses the Microsoft monitoring agent or Monitor agent.

### Microsoft Entra ID authentication and audit logs

Microsoft Entra ID is the first-line authentication solution for Azure Virtual Desktop no matter which connection method a client uses. As a result, it's important to collect Microsoft Entra ID authentication and audit logs. You can collect these logs in the following ways:

- In your diagnostics settings, configure audit logs and sign-in logs to be sent to Log Analytics, where the data can be queried and alerted on.
- Use a connector in Microsoft Sentinel to collect data from Microsoft Entra ID and stream it into Microsoft Sentinel.

### Security event logs

You should collect security event logs from your Azure Virtual Desktop session hosts. It's a good idea to add these logs to a centralized repository for security events that involve your Azure Virtual Desktop hosts. You can use the repository for storing and querying the logs. You can use one of the following options to collect the logs:

- Use a Monitor agent data collection rule for collecting security event logs. This option is recommended.
- Use a Microsoft monitoring agent to collect the logs for Microsoft Sentinel, or use a legacy agent connector to collect security events.
- Use a Microsoft monitoring agent to collect security events for Defender for Cloud.

### Maintain compliance

Monthly security updates are critical for the overall health and security of your Azure Virtual Desktop environment. Ensure that you regularly update your Azure Virtual Desktop environment by installing new images or by using an update management tool. It's best to conduct monthly compliance reporting and monitoring of your environment's overall update compliance. This practice helps ensure that your Azure Virtual Desktop administrators and security team stay aware of the overall security compliance status of your environment.

##### Recommendations

- Use Defender for Cloud to manage vulnerabilities and to assess compliance with common frameworks.
- Use Defender for Cloud to strengthen the overall security posture of your environment.
- Collect Microsoft Entra ID authentication and audit logs.
- Store security event logs from your Azure Virtual Desktop session hosts in a repository.
- Update your Azure Virtual Desktop environment regularly.
- Conduct monthly compliance reporting.

## Reporting

*Impact: Operational Excellence*

Multiple options are available for Azure Virtual Desktop reporting:

- Azure Virtual Desktop Insights. This dashboard provides many of the required insights and visualizations that you need to understand and monitor your Azure Virtual Desktop environment.
- Workbooks and external reporting tools. In some scenarios, it's helpful to have a custom workbook and dashboard. You can create your own reports or workbooks by using Log Analytics tables and Azure Resource Graph query results as data sources. Resource Graph is a service that you can use to report on Azure Virtual Desktop objects such as host pools, workspaces, compute components, and storage solutions. Data is only populated in custom solutions if you turn on diagnostics for Azure Virtual Desktop objects and if you use a supported monitoring agent to collect performance and event log data. The following Log Analytics tables are available as data sources:
  - Azure Virtual Desktop Log Analytics tables
    - `WVDAgentHealthStatus`
    - `WVDCheckpoints`
    - `WVDConnectionGraphicsDataPreview`
    - `WVDConnectionNetworkData`
    - `WVDConnections`
    - `WVDErrors`
    - `WVDFeeds`
    - `WVDHostRegistrations`
    - `WVDManagement`
  - Performance counters table
    - `Perf`
    - `InsightMetrics` (only if the VM insights feature is enabled)
  - Events tables
    - `Event`

##### Recommendations

- Consider using Azure Virtual Desktop Insights for reporting.
- Use Log Analytics tables and Resource Graph query results as data sources when you make custom dashboards.

## Alerting

*Impact: Performance Efficiency, Operational Excellence*

Use the Monitor alerts framework or an equivalent monitoring solution to stay informed about Azure Virtual Desktop performance and security. You can configure custom alerts for the following types of Azure Virtual Desktop events, diagnostics, and resources:

- VM performance
- Critical events, such as application events with IDs 3702 or 3703 for unavailable-state problems on session hosts
- Service Health
- Resource Health
- Azure Virtual Desktop diagnostic data
- Profile and App Attach packages
- Defender for Cloud

##### Recommendations

- Use alerts to stay informed about Azure Virtual Desktop performance and security.
- Configure alerts for various Azure Virtual Desktop events, diagnostics, and resources.

## Next steps

Now that you've looked at observability best practices in Azure Virtual Desktop, explore mechanisms, tools, and perimeters that you can use to further secure your Azure Virtual Desktop workloads.

> [!div class="nextstepaction"]
> [Security and identity and access management (IAM)](./security.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)