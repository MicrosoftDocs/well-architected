---
title: Azure Well-Architected Framework review - Log Analytics
description: Provides architectural best practices according to Azure Well-Architected Framework for Log Analytics workspaces in Azure Monitor. 
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2023
ms.reviewer: bwren
---

# Azure Well-Architected Framework review - Log Analytics
This article provides architectural best practices for [Log Analytics workspaces](/azure/azure-monitor/logs/log-analytics-workspace-overview) in [Azure Monitor](/azure/azure-monitor/overview). The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

This article assumes that you understand system design principles and have a working knowledge of Log Analytics workspaces and features in Azure Monitor that populate it with data. For more information, see [Log Analytics workspace overview](/azure/azure-monitor/logs/log-analytics-workspace-overview).

## Prerequisites
Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

 In order to maintain their functionality and performance, workloads need to be monitored in diverse ways and for diverse reasons. Log Analytics workspaces are a common sink for a significant portion of that monitoring data and support multiple features in Azure Monitor including ad-hoc queries, visualizations, and alerts. Start with [Monitoring and diagnostics guidance](/azure/architecture/best-practices/monitoring) which presents general monitoring principles and identifies the different types of data and required analysis supported by Azure Monitor and enabled by data stored in the workspace.

## Reliability
[Reliability](../reliability/index.yml) refers a workload's resiliency to malfunction and its ability to return to a fully functioning state after a failure occurs. Instead of trying to prevent failures altogether in the cloud, the goal is to minimize the effects of a single failing component.

As Log Analytics workspace is a regional resource, the primary reliability situation to consider is the availability of the workspace and protection of collected data in the case of failure of an Azure datacenter or region. There's currently no standard feature for failover between workspaces in different regions, but there are strategies that you can use if you have particular requirements for availability or compliance.

The reliability situations to consider for Log Analytics workspaces are availability of the workspace and protection of collected data in the rare case of failure of an Azure datacenter or region. There's currently no standard feature for failover between workspaces in different regions, but there are strategies that you can use if you have particular requirements for availability or compliance.


### Design checklist
> - Prefer deploying to regions that support workspace data resilience
> - Make a regional selection based on workload requirements.
> - If you collect enough data for a dedicated cluster, create a dedicated cluster with availability zone support.
> - If you require the workspace to be available in the case of a region failure, or you don't collect enough data for a dedicated cluster, configure data collection to send critical data to multiple workspaces in different regions.
> - If you require data to be protected in the case of datacenter or region failure, configure data export from the workspace to save data in an alternate location.
> - For mission-critical workloads requiring high availability, consider implementing a federated workspace model.
> - Use data collection endpoints (DCE) in the same region as the Log Analytics workspace.
> - Monitor the health of your Log Analytics workspaces.
 
### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Prefer deploying to regions that support workspace data resilience | To support the durability of workspace data, Log Analytics workspaces should be deployed into a [supported region](/azure/azure-monitor/logs/availability-zones#data-resilience---supported-regions). Data resilience is only possible through linking of the workspace to a dedicated cluster in the same region. If you don't collect enough data now to justify a dedicated cluster, this preemptive regional choice will support future change. |
| Make a regional selection based on workload requirements. | To decrease your requirement on multiple regions, it's preferable to have your workload send its telemetry to a workspace in the same region. If you have workloads across regions though, then this could result in multiple workspaces which increases the complexity of your environment. Balance the criteria detailed in [Design a Log Analytics workspace architecture](/azure/azure-monitor/logs/workspace-design) with your availability requirements. |
| If you collect enough data for a dedicated cluster, create a dedicated cluster with availability zone support. | Workspaces linked to a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters) located in a region that supports [availability zones](/azure/azure-monitor/logs/availability-zones#data-resilience---supported-regions) remain available if a datacenter fails.<br><br> A dedicated cluster requires a commitment of at least 500 GB per day from all workspaces in the same region. If you don't collect this much data, then you need to weight the cost of this commitment with reliability features that it provides. |
| If you require the workspace to be available in the case of a region failure, or you don't collect enough data for a dedicated cluster, configure data collection to send critical data to multiple workspaces in different regions. | Configure your data sources to send to multiple workspaces in different regions. For example, configure DCRs for multiple workspaces for Azure Monitor agent running on virtual machines, and multiple diagnostic settings to collection resource logs from Azure resources. <br><br>Even though the data will be available in the alternate workspace in case of failure, resources that rely on the data such as alerts and workbooks wouldn't know to use this workspace. Consider storing ARM templates for critical resources with configuration for the alternate workspace in Azure DevOps or as disabled [policies](/azure/governance/policy/overview) that can quickly be enabled in a failover scenario.<br><br>Tradeoff: This configuration results in duplicate ingestion and retention charges so only use it for critical data. |
| If you require data to be protected in the case of datacenter or region failure, configure data export from the workspace to save data in an alternate location. | The [data export feature of Azure Monitor](/azure/azure-monitor/logs/logs-data-export.md) allows you to continuously export data sent to specific tables to Azure storage where it can be retained for extended periods. Use [Azure Storage redundancy options](../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region), including GRS and GZRS, to replicate this data to other regions. If you require export of [tables that aren't supported by data export](/azure/azure-monitor/logs/logs-data-export.md?tabs=portal#limitations), you can use other methods of exporting data, including Logic apps, to protect your data. This is primarily a solution to meet compliance for data retention since the data can be difficult to analyze and restore to the workspace.<br><br>This option is similar to the previous option of multicasting the data to different workspaces, but has a lower cost because the extra data is written to storage.<br><br> Data export is susceptible to regional incidents because it relies on the stability of the Azure Monitor ingestion pipeline in your region. It doesn't provide resiliency against incidents impacting the regional ingestion pipeline.|
| For mission-critical workloads requiring high availability, consider implementing a federated workspace model that uses multiple workspaces to provide high availability in the case of regional failure. | [Mission-critical](/azure/well-architected/mission-critical/mission-critical-overview) provides prescriptive best practice guidance for architecting highly reliable applications on Azure. The design methodology includes a federated workspace model with multiple Log Analytics workspaces to deliver [high availability](/azure/well-architected/mission-critical/mission-critical-design-methodology#select-a-reliability-tier) in the case of multiple failures, including the failure of an Azure region.<br><br> This strategy eliminates egress costs across regions and remains operational with a region failure, but it requires additional complexity that you must manage with configuration and processes described in [Health modeling and observability of mission-critical workloads on Azure](/azure/well-architected/mission-critical/mission-critical-health-modeling).|
| Use data collection endpoints (DCE) in the same region as the Log Analytics workspace. | DCEs are used by the Azure Monitor agent and the Logs ingestion API to send data to a Log Analytics workspace. You may need multiple DCEs even though your deployment only has a single workspace. See [How to set up data collection endpoints based on your deployment](/azure/azure-monitor/essentials/data-collection-endpoint-overview#how-to-set-up-data-collection-endpoints-based-on-your-deployment) for details on how to configure DCEs for your particular environment. |
| Monitor the health of your Log Analytics workspaces. | Use [Log Analytics workspace insights](/azure/azure-monitor/logs/workspace-design.md) to track failed queries and create [health status alert](/azure/azure-monitor/logs/log-analytics-workspace-health.md#view-log-analytics-workspace-health-and-set-up-health-status-alerts) to proactively notify you if a workspace becomes unavailable because of a datacenter or regional failure. |

### Azure Policy

Azure offers no policies related to reliability of Log Analytics workspaces. [Custom policies](/azure/governance/policy/tutorials/create-custom-policy-definition) can be created to build compliance guardrails around your workspace deployments, such as ensuring workspaces are associated to a dedicated cluster.

While not directly related to the reliability of Log Analytics workspaces themselves, there are Azure policies for nearly every service available to ensure that diagnostics settings are enabled for that service; validating that the service's log data is flowing into a Log Analytics workspace. All services in workload architecture should be sending their log data to a Log Analytics workspace for their own reliability needs, and these policies can help enforce this. Likewise, policies exist to ensure agent-based platforms, such as virtual machines and Kubernetes, have the agent installed.

### Azure Advisor

Azure offers no Azure Advisor recommendations related to the reliability of Log Analytics workspaces.


## Security

[Security](../security/index.yml) is one of the most important aspects of any architecture. Azure Monitor provides features to employ both the principle of least privilege and defense-in-depth. Use the following information to maximize the security of your Log Analytics workspaces and ensure that only authorized clients can insert data and only authorized users access that collected data.

### Design checklist

> [!div class="checklist"]
> - Determine whether to combine your operational data and your security data in the same Log Analytics workspace.
> - Configure access for different types of data in the workspace required for different roles in your organization.
> - Consider using Azure private link to remove access to your workspace from public networks.
> - Export audit data for long term retention or immutability.
> - Use customer managed keys if you require your own encryption key to protect data and saved queries in your workspaces.
> - Configure log query auditing to track which users are running queries.
> - Implement double encryption for the workspace.
> - Determine a strategy to filter or obfuscate sensitive data in your workspace.
> - Purge sensitive data that was accidentally collected.
> - Use Entra ID instead of API key for Query API access. 


### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Determine whether to combine your operational data and your security data in the same Log Analytics workspace. | This is not directly a security concern for Log Analytics itself, but instead a workload data segmentation recommendation. Your decision whether to combine this data depends on your particular security requirements. Combining them in a single workspace gives you better visibility across all your data, although your security team may require a dedicated workspace. See [Design a Log Analytics workspace strategy](/azure/azure-monitor/logs/workspace-design) for details on making this decision for your environment balancing it with criteria in other pillars.<br><br>Tradeoff: There are potential cost implications to enabling Sentinel in your workspace. See details in [Design a Log Analytics workspace architecture](/azure/azure-monitor/logs/workspace-design). |
| Configure access for different types of data in the workspace required for different roles in your organization. | Set the [access control mode](/azure/azure-monitor/logs/manage-access#access-control-mode) for the workspace to *Use resource or workspace permissions* to allow resource owners to use [resource-context](/azure/azure-monitor/logs/manage-access#access-mode) to access their data without being granted explicit access to the workspace. This simplifies your workspace configuration and helps to ensure users will not be able to access data they shouldn't.<br><br>Assign the appropriate [built-in role](/azure/azure-monitor/logs/manage-access#azure-rbac) to grant workspace permissions to administrators at either the subscription, resource group, or workspace level depending on their scope of responsibilities.<br><br>Leverage [table level RBAC](/azure/azure-monitor/logs/manage-access#set-table-level-read-access) for users who require access to a set of tables across multiple resources. Users with table permissions have access to all the data in the table regardless of their resource permissions.<br><br>See [Manage access to Log Analytics workspaces](/azure/azure-monitor/logs/manage-access) for details on the different options for granting access to data in the workspace. |
| Export audit data for long term retention or immutability. | You may have collected audit data in your workspace that's subject to regulations requiring its long term retention. Data in a Log Analytics workspace can't be altered, but it can be [purged](/azure/azure-monitor/logs/personal-data-mgmt#exporting-and-deleting-personal-data). Use [data export](/azure/azure-monitor/logs/logs-data-export) to send data to an Azure storage account with [immutability policies](/azure/storage/blobs/immutable-policy-configure-version-scope) to protect against data tampering. Not every type of log has the same relevance for compliance, auditing, or security, so determine the specific data types that should be exported. |
| Use customer managed keys if you require your own encryption key to protect data and saved queries in your workspaces. | Azure Monitor ensures that all data and saved queries are encrypted at rest using Microsoft-managed keys (MMK). If you require your own encryption key and collect enough data for a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters), use [customer-managed key](/azure/azure-monitor/logs/customer-managed-keys) for greater flexibility and key lifecycle control. If you use Microsoft Sentinel, then make sure that you're familiar with the considerations at [Set up Microsoft Sentinel customer-managed key](/azure/sentinel/customer-managed-keys#considerations).  |
| Export audit data for long term retention or immutability. | You may have collected audit data in your workspace that's subject to regulations requiring its long term retention. Data in a Log Analytics workspace can’t be altered, but it can be [purged](/azure/azure-monitor/logs/personal-data-mgmt#exporting-and-deleting-personal-data). Use [data export](/azure/azure-monitor/logs/logs-data-export) to send data to an Azure storage account with [immutability policies](/azure/storage/blobs/immutable-policy-configure-version-scope) to protect against data tampering. Not every type of logs has the same relevance for compliance, auditing, or security, so determine the specific data types that should be exported. |
| Configure log query auditing to track which users are running queries. | [Log query auditing](/azure/azure-monitor/logs/query-audit) records the details for each query that's run in a workspace. Treat this audit data as security data and secure the [LAQueryLogs](/azure/azure-monitor/reference/tables/laquerylogs) table appropriately. Configure the audit logs for each workspace to be sent to the local workspace, or consolidate in a dedicated security workspace if you separate your operational and security data. Use [Log Analytics workspace insights](/azure/azure-monitor/logs/log-analytics-workspace-insights-overview) to periodically review this data and consider creating log query alert rules to proactively notify you if unauthorized users are attempting to run queries. |
| Implement double encryption for the workspace. | [Double encryption](/azure/storage/common/storage-service-encryption#doubly-encrypt-data-with-infrastructure-encryption) provides an extra layer of encryption to protect against a scenario where one of the encryption algorithms or keys may be compromised. This feature requires a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters) which has a daily minimum ingestion limit.  |
| Determine a strategy to filter or obfuscate sensitive data in your workspace. | You may be collecting data that includes [sensitive information](/azure/azure-monitor/logs/personal-data-mgmt). Filter records that shouldn't be collected using the configuration for the particular data source. Use a [transformation](/azure/azure-monitor/essentials/data-collection-transformations) if only particular columns in the data should be removed or obfuscated.<br><br>If you have standards that require the original data to be unmodified, then you can use the ['h' literal](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) in KQL queries to obfuscate query results displayed in workbooks. |
| Purge sensitive data that was accidentally collected. | Check periodically for private data that may have been accidentally collected in your workspace and use [data purge](/azure/azure-monitor/logs/personal-data-mgmt#exporting-and-deleting-personal-data) to remove it. |
| Use Entra ID instead of API key for Query API access. | [API key-based access](/azure/azure-monitor/logs/api/overview#api-key-authentication-for-sample-data) to the Query APIs does not leave a per-client audit trail. Use sufficiently scoped [Entra ID-based access](/azure/azure-monitor/logs/api/overview#microsoft-entra-authentication-for-workspace-data) so that programmatic query access can be properly audited. |

### Azure Policy
Azure offers some policies related to the security of Log Analytics workspaces to help enforce your desired security posture. Examples of such policies are:
- [Azure Monitor Logs clusters should be encrypted with customer-managed key](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f68a601-6e6d-4e42-babf-3f643a047ea2)
- [Azure Monitor Logs clusters should be created with infrastructure-encryption enabled (double encryption)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea0dfaed-95fb-448c-934e-d6e713ce393d)
- [Saved-queries in Azure Monitor should be saved in customer storage account for logs encryption](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa298e57-9444-42ba-bf04-86e8470e32c7)
- [Log Analytics Workspaces should block non-Azure Active Directory based ingestion](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94c1f94d-33b0-4062-bd04-1cdc3e7eece2)

Azure also offers numerous policies to help enforce private link configuration, such as [Log Analytics workspaces should block log ingestion and querying from public networks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c53d030-cc64-46f0-906d-2bc061cd1334) or even configuring the solution through DINE policies such as [Configure Azure Monitor Private Link Scope to use private DNS zones](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F437914ee-c176-4fff-8986-7e05eb971365).

### Azure Advisor
Azure offers no Azure Advisor recommendations related to the security of Log Analytics workspaces.

## Cost optimization

[Cost optimization](../cost-optimization/index.yml) refers to ways to reduce unnecessary expenses and improve operational efficiencies in your workload. You can significantly reduce your cost for Azure Monitor by understanding your different configuration options and opportunities to reduce the amount of data that it collects. See [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/cost-usage) to understand how data charges are calculated for your Log Analytics workspaces.


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

### Azure Policy
Azure offers no policies related to cost optimization of Log Analytics workspaces. [Custom policies](/azure/governance/policy/tutorials/create-custom-policy-definition) can be created to build compliance guardrails around your workspace deployments, such as ensuring workspaces have desired retention settings.

### Azure Advisor
Azure Advisor will make recommendations to move specific tables in a workspace to the low-cost Basic log data plan for tables that receive relatively high ingestion volume. Understand the limitations using Basic logs before switching. See, [When should I use Basic logs?](/azure/azure-monitor/logs/basic-logs-configure?tabs=portal-1#when-should-i-use-basic-logs)
Azure Advisor will also recommend [changing pricing commitment tier](/azure/azure-monitor/logs/change-pricing-tier) for the whole workspace based on overall usage volume.

## Operational excellence
[Operational excellence](../operational-excellence/index.yml) refers to ensuring workload quality through standardized processes and team cohesion, defining operational procedures for development practices, observability, and release management. Use the following information to minimize the operational toil for Log Analytics workspaces in your workload.

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

### Azure Policy and Azure Advisor
Azure offers no policies nor Azure Advisor recommendations related to the operational excellence of Log Analytics workspaces.

## Performance efficiency
[Performance efficiency](../performance-efficiency/index.yml) is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. Both the workload holistically and individual components in the workload need to be tuned to achieve an efficient status.

It's important to learn the fundamentals of [log data ingestion latency in Azure Monitor](/azure/azure-monitor/logs/data-ingestion-time), as understanding these components will provide you with knowledge about what is in your control and what is out of your control for log collection performance.

Use the following information to ensure that your Log Analytics workspaces and log queries are configured for the performance required by your workload and operations.

### Design checklist

> [!div class="checklist"]
> - Configure log query auditing and use Log Analytics workspace insights to identify slow and inefficient queries.
> - Understand service limits for Log Analytics workspaces.

### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Configure log query auditing and use Log Analytics workspace insights to identify slow and inefficient queries. | [Log query auditing](/azure/azure-monitor/logs/query-audit) stores the compute time required to run each query and the time until results are returned. [Log Analytics workspace insights](/azure/azure-monitor/logs/log-analytics-workspace-insights-overview#query-audit-tab) uses this data to list potentially inefficient queries in your workspace. Consider rewriting these queries to improve their performance. Refer to [Optimize log queries in Azure Monitor](/azure/azure-monitor/logs/query-optimization) for guidance on optimizing your log queries. |
| Understand service limits for Log Analytics workspaces. | In certain high traffic implementations, you may run into service limits that affect your performance and will affect your workspace or workload design. For example, the query API limits the number of records and data volume returned by a query; the Logs ingestion API limits the size of each API call. See [Azure Monitor service limits](/azure/azure-monitor/service-limits#logs-ingestion-api) for a complete list of limits for Azure Monitor and [Log Analytics workspaces](/azure/azure-monitor/service-limits#log-analytics-workspaces) for limits specific to the workspace itself.  |

### Azure Policy and Azure Advisor
Azure offers no policies nor Azure Advisor recommendations related to the performance of Log Analytics workspaces.

## Next step

- [Get best practices for a complete deployment of Azure Monitor](/azure/azure-monitor/best-practices).
