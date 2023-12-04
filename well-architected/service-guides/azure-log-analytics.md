---
title: Provides a template for a Well-Architected Framework (WAF) article that is specific to Log Analytics workspaces.
description: Provides a template for a Well-Architected Framework (WAF) article specific to Log Analytics workspaces in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2023
ms.reviewer: bwren
---

# Azure Well-Architected Framework review - Log Analytics
This article provides architectural best practices for [Log Analytics workspaces](/azure/azure-monitor/logs/log-analytics-workspace-overview) in [Azure Monitor](/azure/azure-monitor/overview). The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

We assume that you understand system design principles, have working knowledge of Azure Monitor, and are well versed with its features. For more information, see [Azure Monitor](/azure/azure-monitor).

## Reliability
[Reliability](/azure/well-architected/resiliency/overview) refers to the ability of a system to recover from failures and continue to function. Instead of trying to prevent failures altogether in the cloud, the goal is to minimize the effects of a single failing component. Use the following information to minimize failure of your Log Analytics workspaces and to protect the data they collect.

The reliability situations to consider for Log Analytics workspaces are availability of the workspace and protection of collected data in the rare case of failure of an Azure datacenter or region. There's currently no standard feature for failover between workspaces in different regions, but there are strategies that you can use if you have particular requirements for availability or compliance.


### Design checklist

> [!div class="checklist"]
> - If you collect enough data for a dedicated cluster, create a dedicated cluster in an availability zone.
> - If you require the workspace to be available in the case of a region failure, or you don't collect enough data for a dedicated cluster, configure data collection to send critical data to multiple workspaces in different regions.
> - If you require data to be protected in the case of datacenter or region failure, configure data export from the workspace to save data in an alternate location.
> - For mission-critical workloads requiring high availability, consider implementing a federated workspace model.
> - Monitor the health of your Log Analytics workspaces.
 
### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| If you collect enough data, create a dedicated cluster in a region that supports availability zones. | Workspaces linked to a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters) located in a region that supports [availability zones](/azure/azure-monitor/logs/availability-zones#data-resilience---supported-regions) remain available if a datacenter fails.<br><br> A dedicated cluster requires a commitment of at least 500 GB per day from all workspaces in the same region. If you don't collect this much data, then you need to weight the cost of this commitment with reliability features that it provides. |
| If you require the workspace to be available in the case of a region failure, or you don't collect enough data for a dedicated cluster, configure data collection to send critical data to multiple workspaces in different regions. | Configure your data sources to send to multiple workspaces in different regions. For example, configure DCRs for multiple workspaces for Azure Monitor agent running on virtual machines, and multiple diagnostic settings to collection resource logs from Azure resources. <br><br>Even though the data will be available in the alternate workspace in case of failure, resources that rely on the data such as alerts and workbooks wouldn't know to use this workspace. Consider storing ARM templates for critical resources with configuration for the alternate workspace in Azure DevOps or as disabled [policies](/azure/governance/policy/overview) that can quickly be enabled in a failover scenario.<br><br>Tradeoff: This configuration results in duplicate ingestion and retention charges so only use it for critical data. |
| If you require data to be protected in the case of datacenter or region failure, configure data export from the workspace to save data in an alternate location. | The [data export feature of Azure Monitor](/azure/azure-monitor/logs/logs-data-export) allows you to continuously export data sent to specific tables to Azure storage where it can be retained for extended periods. Use [Azure Storage redundancy options](/azure/storage/common/storage-redundancy#redundancy-in-a-secondary-region) including GRS and GZRS to replicate this data to other regions. If you require export of [tables that aren't supported by data export](/azure/azure-monitor/logs/logs-data-export?tabs=portal#limitations) then you can use other methods of exporting data including Logic apps to protect your data. This is primarily a solution to meet compliance for data retention since the data can be difficult to analyze and restore back to the workspace.<br><br>This option is similar to the previous option of multicasting the data to different workspaces but has a lower cost since the extra data is being written to storage. |
| For mission-critical workloads requiring high availability, consider implementing a federated workspace model that uses multiple workspaces to provide high availability in the case of regional failure. | [Mission-critical](/azure/well-architected/mission-critical/mission-critical-overview) provides prescriptive best practice guidance for architecting highly-reliable applications on Azure. The design methodology includes a federated workspace model with multiple Log Analytics workspaces to deliver [high availability](/azure/well-architected/mission-critical/mission-critical-design-methodology#select-a-reliability-tier) in the case of multiple failures, including the failure of an Azure region.<br><br> This strategy eliminates egress costs across regions and remains operational with a region failure, but it requires additional complexity that you must manage with configuration and processes described in [Health modeling and observability of mission-critical workloads on Azure](/azure/well-architected/mission-critical/mission-critical-health-modeling). |
| Monitor the health of your Log Analytics workspaces. | Use [Log Analytics workspace insights](/azure/azure-monitor/logs/workspace-design) to track failed queries and create [health status alert](/azure/azure-monitor/logs/log-analytics-workspace-health#view-log-analytics-workspace-health-and-set-up-health-status-alerts) to proactively notify you if a workspace becomes unavailable because of a datacenter or regional failure. |


## Security
[Security](/azure/well-architected/security/overview) is one of the most important aspects of any architecture. Azure Monitor provides features to employ both the principle of least privilege and defense-in-depth. Use the following information to maximize the security of your Log Analytics workspaces and ensure that only authorized users access collected data.

### Design checklist

> [!div class="checklist"]
> - Determine whether to combine your operational data and your security data in the same Log Analytics workspace.
> - Configure access for different types of data in the workspace required for different roles in your organization.
> - Consider using Azure private link to remove access to your workspace from public networks.
> - Use customer managed keys if you require your own encryption key to protect data and saved queries in your workspaces.
> - Export audit data for long term retention or immutability.
> - Configure log query auditing to track which users are running queries.
> - Determine a strategy to filter or obfuscate sensitive data in your workspace.
> - Purge sensitive data that was accidentally collected.


### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Determine whether to combine your operational data and your security data in the same Log Analytics workspace. | Your decision whether to combine this data depends on your particular security requirements. Combining them in a single workspace gives you better visibility across all your data, although your security team may require a dedicated workspace. See [Design a Log Analytics workspace strategy](/azure/azure-monitor/logs/workspace-design) for details on making this decision for your environment balancing it with criteria in other pillars.<br><br>Tradeoff: There are potential cost implications to enabling Sentinel in your workspace. See details in [Design a Log Analytics workspace architecture](/azure/azure-monitor/logs/workspace-design). |
| Configure access for different types of data in the workspace required for different roles in your organization. | Set the [access control mode](/azure/azure-monitor/logs/manage-access#access-control-mode) for the workspace to *Use resource or workspace permissions* to allow resource owners to use [resource-context](/azure/azure-monitor/logs/manage-access#access-mode) to access their data without being granted explicit access to the workspace. This simplifies your workspace configuration and helps to ensure users will not be able to access data they shouldn't.<br><br>Assign the appropriate [built-in role](/azure/azure-monitor/logs/manage-access#azure-rbac) to grant workspace permissions to administrators at either the subscription, resource group, or workspace level depending on their scope of responsibilities.<br><br>Leverage [table level RBAC](/azure/azure-monitor/logs/manage-access#set-table-level-read-access) for users who require access to a set of tables across multiple resources. Users with table permissions have access to all the data in the table regardless of their resource permissions.<br><br>See [Manage access to Log Analytics workspaces](/azure/azure-monitor/logs/manage-access) for details on the different options for granting access to data in the workspace. |
| Consider using Azure private link to remove access to your workspace from public networks. | Connections to public endpoints are secured with end-to-end encryption. If you require a private endpoint, you can use [Azure private link](/azure/azure-monitor/logs/private-link-security) to allow resources to connect to your Log Analytics workspace through authorized private networks. Private link can also be used to force workspace data ingestion through ExpressRoute or a VPN. See [Design your Azure Private Link setup](/azure/azure-monitor/logs/private-link-design) to determine the best network and DNS topology for your environment. |
| Use customer managed keys if you require your own encryption key to protect data and saved queries in your workspaces. | Azure Monitor ensures that all data and saved queries are encrypted at rest using Microsoft-managed keys (MMK). If you require your own encryption key and collect enough data for a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters), use [customer-managed key](/azure/azure-monitor/logs/customer-managed-keys) for greater flexibility and key lifecycle control. If you use Microsoft Sentinel, then make sure that you're familiar with the considerations at [Set up Microsoft Sentinel customer-managed key](/azure/sentinel/customer-managed-keys#considerations).  |
| Export audit data for long term retention or immutability. | You may have collected audit data in your workspace that's subject to regulations requiring its long term retention. Data in a Log Analytics workspace can’t be altered, but it can be [purged](/azure/azure-monitor/logs/personal-data-mgmt#exporting-and-deleting-personal-data). Use [data export](/azure/azure-monitor/logs/logs-data-export) to send data to an Azure storage account with [immutability policies](/azure/storage/blobs/immutable-policy-configure-version-scope) to protect against data tampering. Not every type of logs has the same relevance for compliance, auditing, or security, so determine the specific data types that should be exported. |
| Configure log query auditing to track which users are running queries. | [Log query auditing](/azure/azure-monitor/logs/query-audit) records the details for each query that's run in a workspace. Treat this audit data as security data and secure the [LAQueryLogs](/azure/azure-monitor/reference/tables/laquerylogs) table appropriately. Configure the audit logs for each workspace to be sent to the local workspace, or consolidate in a dedicated security workspace if you separate your operational and security data. Use [Log Analytics workspace insights](/azure/azure-monitor/logs/log-analytics-workspace-insights-overview) to periodically review this data and consider creating log query alert rules to proactively notify you if unauthorized users are attempting to run queries. |
| Determine a strategy to filter or obfuscate sensitive data in your workspace. | You may be collecting data that includes [sensitive information](/azure/azure-monitor/logs/personal-data-mgmt). Filter records that shouldn't be collected using the configuration for the particular data source. Use a [transformation](/azure/azure-monitor/essentials/data-collection-transformations) if only particular columns in the data should be removed or obfuscated.<br><br>If you have standards that require the original data to be unmodified, then you can use the ['h' literal](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) in KQL queries to obfuscate query results displayed in workbooks. |
| Purge sensitive data that was accidentally collected. | Check periodically for private data that may have been accidentally collected in your workspace and use [data purge](/azure/azure-monitor/logs/personal-data-mgmt#exporting-and-deleting-personal-data) to remove it. |



## Cost optimization
[Cost optimization](/azure/well-architected/cost/overview) refers to ways to reduce unnecessary expenses and improve operational efficiencies. You can significantly reduce your cost for Azure Monitor by understanding your different configuration options and opportunities to reduce the amount of data that it collects. See [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/cost-usage) to understand how data charges are calculated for your Log Analytics workspaces.

### Design checklist

> [!div class="checklist"]
> - Determine whether to combine your operational data and your security data in the same Log Analytics workspace.
> - Configure pricing tier for the amount of data that each Log Analytics workspace typically collects.
> - Configure data retention and archiving.
> - Configure tables used for debugging, troubleshooting, and auditing as Basic Logs.
> - Limit data collection from data sources for the workspace.
> - Regularly analyze collected data to identify trends and anomalies.
> - Create an alert when data collection is high.
> - Consider a daily cap as a preventative measure to ensure that you don't exceed a particular budget.

### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Determine whether to combine your operational data and your security data in the same Log Analytics workspace. | Since all data in a Log Analytics workspace is subject to Microsoft Sentinel pricing if Sentinel is enabled, there may be cost implications to combining this data. See [Design a Log Analytics workspace strategy](/azure/azure-monitor/logs/workspace-design) for details on making this decision for your environment balancing it with criteria in other pillars. |
| Configure pricing tier for the amount of data that each Log Analytics workspace typically collects. | By default, Log Analytics workspaces will use pay-as-you-go pricing with no minimum data volume. If you collect enough data, you can significantly decrease your cost by using a [commitment tier](/azure/azure-monitor/logs/cost-logs#commitment-tiers), which allows you to commit to a daily minimum of data collected in exchange for a lower rate. If you collect enough data across workspaces in a single region, you can link them to a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters) and combine their collected volume using [cluster pricing](/azure/azure-monitor/logs/cost-logs#dedicated-clusters).<br><br>See [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/logs/cost-logs) for details on commitment tiers and guidance on determining which is most appropriate for your level of usage. See [Usage and estimated costs](/azure/azure-monitor/usage-estimated-costs#usage-and-estimated-costs) to view estimated costs for your usage at different pricing tiers.  |
| Configure data retention and archiving. | There is a charge for retaining data in a Log Analytics workspace beyond the default of 31 days (90 days if Sentinel is enabled on the workspace and 90 days for Application insights data). Consider your particular requirements for having data readily available for log queries. You can significantly reduce your cost by configuring [Archived Logs](/azure/azure-monitor/logs/data-retention-archive), which allows you to retain data for up to seven years and still access it occasionally using [search jobs](/azure/azure-monitor/logs/search-jobs) or [restoring a set of data](/azure/azure-monitor/logs/restore) to the workspace. |
| Configure tables used for debugging, troubleshooting, and auditing as Basic Logs. | Tables in a Log Analytics workspace configured for [Basic Logs](/azure/azure-monitor/logs/basic-logs-configure) have a lower ingestion cost in exchange for limited features and a charge for log queries. If you query these tables infrequently and don't use them for alerting, this query cost can be more than offset by the reduced ingestion cost. |
| Limit data collection from data sources for the workspace. | The primary factor for the cost of Azure Monitor is the amount of data that you collect in your Log Analytics workspace, so you should ensure that you collect no more data that you require to assess the health and performance of your services and applications. See [Design a Log Analytics workspace architecture](/azure/azure-monitor/logs/workspace-design) for details on making this decision for your environment balancing it with criteria in other pillars.<br><br>Tradeoff: There may be a tradeoff between cost and your monitoring requirements. For example, you may be able to detect a performance issue more quickly with a high sample rate, but you may want a lower sample rate to save costs. Most environments will have multiple data sources with different types of collection, so you need to balance your particular requirements with your cost targets for each. See [Cost optimization in Azure Monitor](/azure/azure-monitor/best-practices-cost) for recommendations on configuring collection for different data sources. |
| Regularly analyze collected data to identify trends and anomalies.  | Use [Log Analytics workspace insights](/azure/azure-monitor/logs/log-analytics-workspace-insights-overview) to periodically review the amount of data collected in your workspace. In addition to helping you understand the amount of data collected by different sources, it will identify anomalies and upward trends in data collection that could result in excess cost. Further analyze data collection using methods in [Analyze usage in Log Analytics workspace](/azure/azure-monitor/logs/analyze-usage) to determine if there's additional configuration that can decrease your usage further. This is particularly important when you add a new set of data sources, such as a new set of virtual machines or onboard a new service. |
| Create an alert when data collection is high. | To avoid unexpected bills, you should be [proactively notified anytime you experience excessive usage](/azure/azure-monitor/logs/analyze-usage#send-alert-when-data-collection-is-high). Notification allows you to address any potential anomalies before the end of your billing period. |
| Consider a daily cap as a preventative measure to ensure that you don't exceed a particular budget. | A [daily cap](/azure/azure-monitor/logs/daily-cap) disables data collection in a Log Analytics workspace for the rest of the day after your configured limit is reached. This shouldn't be used as a method to reduce costs as described in [When to use a daily cap](/azure/azure-monitor/logs/daily-cap#when-to-use-a-daily-cap).<br><br>If you do set a daily cap, in addition to [creating an alert when the cap is reached](/azure/azure-monitor/logs/log-analytics-workspace-health#view-log-analytics-workspace-health-and-set-up-health-status-alerts), ensure that you also [create an alert rule to be notified when some percentage has been reached (90% for example)](/azure/azure-monitor/logs/analyze-usage#send-alert-when-data-collection-is-high). This gives you an opportunity to investigate and address the cause of the increased data before the cap shuts off data collection. |



## Operational excellence
[Operational excellence](/azure/well-architected/devops/overview) refers to operations processes required keep a service running reliably in production. Use the following information to minimize the operational requirements for supporting Log Analytics workspaces.

### Design checklist

> [!div class="checklist"]
> - Design a workspace architecture with the minimal number of workspaces to meet your business requirements.
> - Use Infrastructure as Code (IaC) when managing multiple workspaces.
> - Use Log Analytics workspace insights to track the health and performance of your Log Analytics workspaces.
> - Create alert rules to be proactively notified of operational issues in the workspace.
> - Ensure that you have a well-defined operational process for data segregation.

### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Design a workspace strategy to meet your business requirements. | See [Design a Log Analytics workspace architecture](/azure/azure-monitor/logs/workspace-design) for guidance on designing a strategy for your Log Analytics workspaces including how many to create and where to place them.<br><br>A single or at least minimal number of workspaces will maximize your operational efficiency since it limits the distribution of your operational and security data, increasing your visibility into potential issues, making patterns easier to identify, and minimizing your maintenance requirements.<br><br>You may have requirements for multiple workspaces such as multiple tenants, or you may need workspaces in multiple regions to support your availability requirements. In these cases, ensure that you have appropriate processes in place to manage this increased complexity. |
| Use Infrastructure as Code (IaC) when managing multiple workspaces. | Use Infrastructure as Code (IaC) to define the details of your workspaces in [ARM](/azure/azure-monitor/logs/resource-manager-workspace), [BICEP](/azure/azure-monitor/logs/resource-manager-workspace), or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/log_analytics_workspace.html). This allows you to you leverage your existing DevOps processes to deploy new workspaces and [Azure Policy](/azure/governance/policy/overview) to enforce their configuration. |
| Use Log Analytics workspace insights to track the health and performance of your Log Analytics workspaces.  | [Log Analytics workspace insights](/azure/azure-monitor/logs/workspace-design) provides a unified view of the usage, performance, health, agents, queries, and change log for all your workspaces. Review this information on a regular basis to track the health and operation of each of your workspaces. |
| Create alert rules to be proactively notified of operational issues in the workspace. | Each workspace has an [operation table](/azure/azure-monitor/logs/monitor-workspace) that logs important activities affecting workspace. Create alert rules based on this table to be proactively notified when an operational issue occurs. You can use [recommended alerts for the workspace](/azure/azure-monitor/logs/log-analytics-workspace-health) to simplify the creation of the most critical alert rules. |
| Ensure that you have a well-defined operational process for data segregation. | You may have different requirements for different types of data stored in your workspace. Make sure that you clearly understand such requirements as data retention and security when [designing your workspace strategy](/azure/azure-monitor/logs/workspace-design) and configuring settings such as [permissions](/azure/azure-monitor/roles-permissions-security) and [archiving](/azure/azure-monitor/logs/data-retention-archive). You should also have a clearly defined process for occasionally [purging](/azure/azure-monitor/logs/personal-data-mgmt#exporting-and-deleting-personal-data) data with personal information that's accidentally collected. |



## Performance efficiency
[Performance efficiency](/azure/well-architected/scalability/overview) is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. Use the following information to ensure that your Log Analytics workspaces and log queries are configured for maximum performance.

### Design checklist

> [!div class="checklist"]
> - Configure log query auditing and use Log Analytics workspace insights to identify slow and inefficient queries.

### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Configure log query auditing and use Log Analytics workspace insights to identify slow and inefficient queries. | [Log query auditing](/azure/azure-monitor/logs/query-audit) stores the compute time required to run each query and the time until results are returned. [Log Analytics workspace insights](/azure/azure-monitor/logs/log-analytics-workspace-insights-overview#query-audit-tab) uses this data to list potentially inefficient queries in your workspace. Consider rewriting these queries to improve their performance. Refer to [Optimize log queries in Azure Monitor](/azure/azure-monitor/logs/query-optimization) for guidance on optimizing your log queries. |
## Next step

- [Get best practices for a complete deployment of Azure Monitor](/azure/azure-monitor/best-practices).
