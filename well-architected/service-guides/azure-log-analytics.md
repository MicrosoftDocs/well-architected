---
title: Architecture Best Practices for Log Analytics
description: Learn about the architectural best practices according to the Azure Well-Architected Framework for Log Analytics workspaces in Azure Monitor. 
ms.topic: concept-article
author: bwren
ms.author: bwren
ms.date: 08/11/2025
ms.reviewer: bwren
azure.category:
  - management-and-governance
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD025 -->

# Architecture best practices for Log Analytics

Log Analytics workspaces in Azure Monitor are centralized repositories for collecting, storing, and analyzing log and performance data from various sources across your Azure environment. These workspaces serve as the primary data sink for monitoring information and support advanced querying, visualization, and alerting capabilities to help you gain insights into your workload's health and performance.

This article assumes that as an architect, you understand the importance of comprehensive monitoring and observability for your workload and have chosen Log Analytics workspaces as part of your monitoring strategy. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- markdownlint-disable-next-line MD036 -->
**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Log Analytics workspaces

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review [service limits for Log Analytics workspaces](/azure/azure-monitor/service-limits#log-analytics-workspaces):** The service limits section explains restrictions on data collection, data retention, and other aspects of the service. These limits help you design an effective workload observability strategy. Make sure that you review [Azure Monitor service limits](/azure/azure-monitor/service-limits) because many functions, such as queries, work in tandem with Log Analytics workspaces.
>
> - **Plan for workspace resilience and recovery:** Log Analytics workspaces are regional and don't have built-in support for cross-regional redundancy or replication. Availability zone redundancy options are limited. Because of these limitations, you should determine the reliability requirements of your workspaces and strategize to meet those targets.
>
>    Your requirements might stipulate that your workspace must be resilient to datacenter failures or regional failures. Or they might stipulate that you must be able to recover your data to a new workspace in a failover region.
>
>    Each of these scenarios require extra resources and processes to be successful, so carefully consider how to balance your reliability targets with cost and complexity.
>
> - **Choose the right deployment regions to meet your reliability requirements:** Deploy your Log Analytics workspace and data collection endpoints (DCEs) co-located with the workload components that emit operational data. Where you [deploy your workload](/azure/cloud-adoption-framework/ready/azure-setup-guide/regions) should inform your choice of the appropriate region in which to deploy your workspace and your DCEs.
>
>    You might need to weigh the regional availability of specific Log Analytics functionality, such as dedicated clusters, against other factors that are more central to your workload's reliability, cost, and performance requirements.
>
> - **Exclude workspaces from critical path dependencies:** Log Analytics workspaces serve as important components of your observability system, but you shouldn't include them in your [workload's critical path](/azure/well-architected/reliability/identify-flows). These workspaces collect and store operational data that's essential for monitoring and troubleshooting. However, the core functionality of your workload must remain independent of workspace availability. This architectural separation ensures that observability system outages don't cascade into workload runtime failures.
>
> - **Ensure that your observability systems are healthy:** Like any other component of your workload, ensure that your monitoring and logging systems are functioning properly. To achieve reliable observability, enable features that send health data signals to your operations teams. Set up health data signals specific to your Log Analytics workspaces and associated resources.

### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| To support high durability of workspace data, deploy Log Analytics workspaces into a [region](/azure/azure-monitor/logs/availability-zones#supported-regions) that supports data resilience. | Data resilience spreads replicas of your log data across availability zones, which provide protection against datacenter outages. |
| Consider linking the workspace to a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters) in the same region. | Even if you don't collect enough data now to justify a dedicated cluster, this preemptive regional choice helps support future growth. |
| Deploy your workspace in the same region as the instances of your workload. Use [DCEs](/azure/azure-monitor/essentials/data-collection-endpoint-overview#how-to-set-up-data-collection-endpoints-based-on-your-deployment) in the same region as the Log Analytics workspace. <br><br> If your workload is deployed in an active-active design, consider using multiple workspaces and DCEs spread across the regions in which your workload is deployed. | Having your workspace and DCEs in the same region as your workload mitigates the risk of impacts by outages in other regions. <br><br> Deploying workspaces in multiple regions adds complexity to your environment but provides better availability for geographically distributed workloads. |
| Configure log multicasting to [send critical data to multiple workspaces](/azure/azure-monitor/agents/data-collection-rule-overview) across different regions when workspace availability during regional failures is required. Set up [data collection rules (DCRs)](/azure/azure-monitor/agents/data-collection-rule-overview) and [diagnostic settings](/azure/azure-monitor/platform/diagnostic-settings#destinations) to duplicate critical log streams to backup workspaces. <br><br> Store Azure Resource Manager templates (ARM templates) for alerting resources with alternate workspace configurations to enable rapid failover. | Log multicasting ensures continuous access to critical operational data for troubleshooting and incident response during regional outages. This access maintains visibility into workload health when primary monitoring infrastructure is unavailable. <br><br> Trade-off: This configuration results in duplicate ingestion and retention charges, so only use it for critical data. |
| If you require data to be protected in a datacenter or region failure, configure [data export](/azure/azure-monitor/logs/logs-data-export) from the workspace to save data in an alternate location. Use [Azure Storage redundancy options](/azure/storage/common/storage-redundancy#redundancy-in-a-secondary-region), including geo-redundant storage (GRS) and geo-zone-redundant storage (GZRS), to further replicate this data to other regions. <br><br> Data export doesn't provide resiliency against incidents that affect the regional ingestion pipeline. If you require the export of [tables not supported by data export](/azure/azure-monitor/logs/logs-data-export#limitations), you can use other methods of exporting data, including Azure Logic Apps, to protect your data. | Historic operational log data might not be readily queryable in the exported state. However, it ensures that the data survives a prolonged regional outage and can be accessed and retained for an extended period. |
| For mission-critical workloads, consider implementing a federated workspace model that uses multiple workspaces to provide high availability if there's a regional failure. <br><br> To implement this approach, follow the guidance described in [Health modeling and observability of mission-critical workloads on Azure](/azure/well-architected/mission-critical/mission-critical-health-modeling) for how to design highly reliable applications on Azure. | The design methodology includes a federated workspace model with multiple Log Analytics workspaces to deliver high availability if there are multiple failures, including the failure of an Azure region. <br><br> This strategy eliminates egress costs across regions, and your workload remains operational during a region failure. |
| Design DCRs with a single responsibility principle to keep DCR rules simple and minimize transformation in DCRs as described in [Best practices for data collection rule creation and management](/azure/azure-monitor/essentials/data-collection-rule-best-practices). <br><br> Use composition of rule assignments to achieve the desired observability scope for the logical target. | When you use narrowly focused DCRs, it minimizes the risk of a rule misconfiguration having a broader effect. It also limits the effect to only the scope for which the DCR was built. <br><br> Transformation can be powerful and necessary in some scenarios, but it can be challenging to test and troubleshoot keyword query language (KQL) work. |
| Configure [daily cap](/azure/azure-monitor/logs/daily-cap) settings to prevent runaway ingestion while ensuring that critical operational data collection continues. Set the cap above your typical daily ingestion volume and create alerts when approaching capacity to investigate before critical data collection stops. <br><br> Establish [data retention policies](/azure/azure-monitor/logs/data-retention-configure) that align with your troubleshooting and incident response requirements. This approach retains critical log types for sufficient periods to support root cause analysis. | Daily caps help ensure continuous availability of essential troubleshooting data during outages and incidents by preventing misconfiguration from disrupting critical log collection needed for rapid incident response. <br><br> Proper retention policies maintain access to historical operational data required for effective root cause analysis, trend identification, and pattern recognition that support reliable workload operations and faster mean time to recovery. |
| Use [Log Analytics Workspace Insights](/azure/azure-monitor/logs/workspace-design) to track ingestion volume, ingested data versus your data cap, unresponsive log sources, and failed queries, among other data. <br><br> Create [health status alerts](/azure/azure-monitor/logs/log-analytics-workspace-health#view-log-analytics-workspace-health-and-set-up-health-status-alerts) to proactively notify you if a workspace becomes unavailable because of a datacenter or regional failure. | Workspace insights help ensure that you can successfully monitor the health of your workspaces and proactively act if workload health is at risk of degradation. <br><br> As with all other components of your workload, it's critical that you're aware of health metrics and can identify trends to improve your reliability over time. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving these goals by applying approaches to the technical design around your monitoring and logging solution.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Review security best practices:** Review best practices for security in the [Azure Monitor security baseline](/security/benchmark/azure/baselines/azure-monitor-security-baseline) and [Manage access to Log Analytics workspaces](/azure/azure-monitor/logs/manage-access) articles.
>
> - **Deploy your workspaces with segmentation as a cornerstone principle:** Implement segmentation at the networking, data, and access levels. Segmentation helps ensure that your workspaces are isolated to the appropriate degree. It also helps protect your workspaces from unauthorized access to the highest degree possible, while still meeting your business requirements for reliability, cost optimization, operational excellence, and performance efficiency.
>
> - **Ensure that you can audit workspace reads and writes activities and associated identities:** Attackers can benefit from viewing operational logs. A compromised identity can lead to log injection attacks. Enable auditing of operations that run from the Azure portal or through API interactions and the associated users.
>
>    If you aren't set up to audit your workspace, you might put your organization at risk of breaching compliance requirements.
>
> - **Implement robust network controls:** Help secure network access to your workspace and your logs through network isolation and firewall functions. Insufficiently configured network controls increase the risk of unauthorized or malicious access.
>
> - **Determine what types of data need immutability or long-term retention:** Your log data should be treated with the same rigor as workload data inside production systems. Include log data in your data classification practices to ensure that you successfully store sensitive log data according to its compliance requirements.
>
> - **Protect log data at rest through encryption:** Segmentation alone won't completely protect the confidentiality of your log data. If unauthorized raw access occurs, encrypting the log data at rest helps prevent bad actors from using that data outside of your workspace.
>
> - **Protect sensitive log data through obfuscation:** Just like workload data that resides in production systems, you must take extra measures to ensure that confidentiality is retained for sensitive information that might be intentionally or unintentionally present in operational logs. When you use obfuscation methods, it helps you hide sensitive log data from unauthorized access.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Use [customer-managed keys](/azure/azure-monitor/logs/customer-managed-keys) to protect data and saved queries in your workspaces when you require control over encryption keys. <br><br> Customer-managed keys require a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters) that has sufficient data volume to be cost-effective. Store your encryption keys in [Azure Key Vault](/azure/key-vault/general/overview) and consider the specific [Microsoft Sentinel requirements](/azure/sentinel/customer-managed-keys#considerations) if you use that service. | Customer-managed keys provide control over the key life cycle and ability to revoke access to your data when regulatory or organizational requirements mandate customer-controlled encryption. |
| Configure [Log query auditing](/azure/azure-monitor/logs/query-audit) to track which users are running queries. Use [Log Analytics Workspace Insights](/azure/azure-monitor/logs/log-analytics-workspace-insights-overview) to periodically review this data. <br><br> Consider creating log query alert rules to proactively notify you when unauthorized users attempt to run queries. | Query auditing records the details for each query run in a workspace and bolsters your security posture by ensuring that unauthorized access is caught immediately if it occurs. |
| Use [private link](/azure/azure-monitor/logs/private-link-security) functionality to limit communications between log sources and your workspaces to private networking. | Private links provide network isolation and let you control which virtual networks can access a given workspace. This approach further enhances your security through segmentation. |
| Use [Microsoft Entra ID](/azure/azure-monitor/logs/api/overview#microsoft-entra-authentication-for-workspace-data) instead of [API keys](/azure/azure-monitor/logs/api/overview#api-key-authentication-for-sample-data) for workspace API access when available. Use sufficiently scoped Microsoft Entra ID-based access for programmatic access. | Microsoft Entra ID authentication provides a per-client audit trail for programmatic access, unlike API key-based access to the query APIs. |
| Set the [access control mode](/azure/azure-monitor/logs/manage-access#access-control-mode) for the workspace to *Use resource or workspace permissions*. This access control lets resource owners use [resource-context](/azure/azure-monitor/logs/manage-access#access-mode) to access their data without being granted explicit access to the workspace. <br><br> Use [table level role-based access control (RBAC)](/azure/azure-monitor/logs/manage-access#set-table-level-read-access) for users who require access to a set of tables across multiple resources. Assign the appropriate [built-in role](/azure/azure-monitor/logs/manage-access#azure-rbac) to grant workspace permissions to administrators at either the subscription, resource group, or workspace level according to scope of their responsibilities. <br><br> See [Manage access to Log Analytics workspaces](/azure/azure-monitor/logs/manage-access) for more information about the various options for granting access to data in the workspace. | Proper access control mode configuration simplifies your workspace configuration and helps ensure that users can't access operational data that they shouldn't. Users that have table permissions can access all the data in the table regardless of their resource permissions. |
| Use [data export](/azure/azure-monitor/logs/logs-data-export) to send data to an Azure Storage account with [immutability policies](/azure/storage/blobs/immutable-policy-configure-version-scope) to help protect against data tampering. <br><br> Determine the specific data types that should be exported based on compliance, auditing, or security requirements, and [purge](/azure/azure-monitor/logs/personal-data-mgmt#export-delete-or-purge-personal-data) data as required. | Data export with immutability policies meets compliance requirements for long-term retention of audit data. Data in a Log Analytics workspace can't be altered, but it can be purged. |
| Filter records that shouldn't be collected by using the configuration for the specific data source. Use a [transformation](/azure/azure-monitor/essentials/data-collection-transformations) if only specific columns in the data should be removed or obfuscated. <br><br> If you have standards that require the original data to be unmodified, you can use the ['h' literal](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) in KQL queries to obfuscate query results displayed in workbooks. | Data filtering and transformation helps ensure that you maintain confidentiality on sensitive information and comply with requirements proactively. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those business goals. They also help you make trade-offs as necessary in the technical design related to your monitoring and logging solution.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload aligns with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Perform cost modeling exercises:** These exercises help you understand your current workspace costs and forecast your costs relative to workspace growth. Analyze your growth trends in your workload and ensure that you understand plans for workload expansion to properly forecast your future operational logging costs.
>
> - **Choose the right billing model:** Use your cost model to determine the best [billing model](/azure/azure-monitor/logs/cost-logs) for your scenario. How you currently use your workspaces and how you plan to use them as your workload evolves determines whether a pay-as-you-go or commitment tier model is best suited for your scenario.
>
>    Remember that you can choose different billing models for each workspace. You can also combine workspace costs in specific cases, so you can be granular in your analysis and decision-making.
>
> - **Collect just the right amount of log data:** Perform regularly scheduled analysis of your diagnostic settings on your resources, data collection rule configuration, and custom application code logging to ensure that you don't collect unnecessary log data.
>
> - **Treat nonproduction environments differently than production environments:** Review your nonproduction environments to ensure that your diagnostic settings and retention policies are configured appropriately. These settings and policies can often be significantly less robust than in production, especially for dev/test or sandbox environments.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Configure the pricing tier for the amount of data that each Log Analytics workspace typically collects. If you collect enough data, use a [commitment tier](/azure/azure-monitor/logs/cost-logs#commitment-tiers) to commit to a daily minimum of data collected in exchange for a lower rate. <br><br> For more information about commitment tiers and guidance about how to determine your appropriate usage level, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/logs/cost-logs). <br><br> To view estimated costs for your usage at different pricing tiers, see [Usage and estimated costs](/azure/azure-monitor/usage-estimated-costs#usage-and-estimated-costs). | Commitment tiers significantly decrease your cost compared to pay-as-you-go pricing when you collect sufficient daily data volumes to meet the minimum commitment thresholds. |
| If you collect enough data across workspaces in a single region, link them to a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters) and combine their collected volume by using [cluster pricing](/azure/azure-monitor/logs/cost-logs#dedicated-clusters). <br><br> Configure the cluster to aggregate ingestion volumes from multiple workspaces to achieve cost-effective pricing tiers. | Dedicated clusters with cluster pricing provide substantial cost savings when you have multiple workspaces in the same region. This setup allows you to combine their data volumes to reach higher commitment tiers and reduce per-gigabyte ingestion costs. |
| Configure [data retention and archiving](/azure/azure-monitor/logs/data-retention-archive). Consider your specific requirements for having data readily available for log queries. <br><br> Configure [archived logs](/azure/azure-monitor/logs/data-retention-archive) to retain data for up to seven years and still access it occasionally through [search jobs](/azure/azure-monitor/logs/search-jobs) or by [restoring a set of data](/azure/azure-monitor/logs/restore) to the workspace. | Data retention and archiving configuration significantly reduces your cost for long-term data retention beyond the default period while maintaining access to historical data when needed. |
| Use Summary rules for high-volume data streams to optimize storage costs.<br><br> [Summary rules](/azure/azure-monitor/logs/summary-rules) let you summarize high-ingestion-rate streams across Analytics, Basic, or Auxiliary plans, providing robust analysis, dashboarding, and long-term reporting experiences on summarized data. Summary rules enable automated data summarization capabilities that significantly reduce storage costs for high-volume log data while maintaining analytical insights through aggregated datasets. | Summary rules provide cost-effective long-term data retention by creating tiered data architectures where high-frequency raw data is summarized for storage optimization. Organizations can balance cost efficiency with analytical requirements by maintaining detailed insights through aggregated datasets while optimizing long-term data retention expenses. |
| If you use Microsoft Sentinel to analyze security logs, consider employing a separate workspace to store those logs. Review [Microsoft Sentinel pricing](/azure/sentinel/billing#how-youre-charged-for-microsoft-sentinel) to understand cost implications. | Separate workspaces help you control costs by separating security logs subject to Microsoft Sentinel pricing from operational logs charged at standard Log Analytics pricing. |
| Configure tables used for debugging, troubleshooting, and auditing as [Basic logs](/azure/azure-monitor/logs/basic-logs-configure). | Basic logs configuration provides lower ingestion costs for infrequently queried tables, where query charges can be offset by reduced ingestion costs. |
| Capture the right amount of data by configuring [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) and [DCRs](/azure/azure-monitor/agents/data-collection-rule-overview) to collect only essential operational data. <br><br> Review each resource's data sources to ensure that you collect data that provides monitoring value while avoiding unnecessary data. See [Cost optimization in Azure Monitor](/azure/azure-monitor/best-practices-cost) for configuration guidance. | Capturing the right amount of data reduces costs by focusing on operationally valuable data while eliminating noise. This approach ensures that you capture essential metrics without paying for data that doesn't contribute to monitoring objectives. |
| Regularly analyze workspace usage data to identify trends and anomalies. Use [Log Analytics Workspace Insights](/azure/azure-monitor/logs/log-analytics-workspace-insights-overview) to periodically review the amount of data collected in your workspace. <br><br> Further analyze data collection by using methods in [Analyze usage in Log Analytics workspace](/azure/azure-monitor/logs/analyze-usage) to determine whether other configurations can further decrease your usage. | Regular usage analysis helps you understand data collection across various sources, identify anomalies and upward trends that might lead to excess costs, and proactively manage expenses when you introduce new data sources. |
| Create an alert when data collection is high. Set up [proactive notifications for excessive usage](/azure/azure-monitor/logs/analyze-usage#send-alert-when-data-collection-is-high). | High data collection alerts enable you to address potential anomalies before the end of your billing period, which helps avoid unexpected bills. |
| Configure a [daily cap](/azure/azure-monitor/logs/daily-cap) to prevent runaway ingestion because of misconfiguration or abuse, as described in [When to use a daily cap](/azure/azure-monitor/logs/daily-cap#when-to-use-a-daily-cap). <br><br> Create alerts to notify you when [the cap is reached](/azure/azure-monitor/logs/log-analytics-workspace-health#view-log-analytics-workspace-health-and-set-up-health-status-alerts) and when a [percentage is reached](/azure/azure-monitor/logs/analyze-usage#send-alert-when-data-collection-is-high), such as 90 percent capacity. | Daily cap configuration provides protection against unexpected budget overruns while giving you opportunity to investigate and address the cause of increased data before critical data collection is shut off. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. 

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Log Analytics workspaces.

> [!div class="checklist"]
>
> - **Use infrastructure as code (IaC) for all functions related to your workload's Log Analytics workspaces:** Minimize the risk of human error that can occur from manually administering and operating your log collection, ingestion, storage, and querying functions, including saved queries and query packs, by automating as many of those functions as possible through code.
>
>    Also, include alerts that report health status changes and the configuration of diagnostic settings for resources that send logs to your workspaces in your IaC code. Include the code with your other workload-related code to ensure that your safe deployment practices are maintained for the management of your workspaces.
>
> - **Ensure that your workspaces are healthy and that you're notified when problems occur:** Like any other component of your workload, your workspaces can encounter problems. These problems can consume valuable time and resources to diagnose and fix, and they might leave your team unaware of the production workload's status. Proactive monitoring of workspaces and early problem mitigation enable operations teams to reduce time spent on troubleshooting and repairs.
>
> - **Separate your production from nonproduction workloads:** Avoid unnecessary complexity that can cause extra work for an operations team by using different workspaces for your production environment than those used by nonproduction environments. Comingled data can also lead to confusion because testing activities might appear to be events in production.
>
> - **Prefer built-in tools and functions over non-Microsoft solutions:** Use built-in tools to extend the functionality of your monitoring and logging systems. You might need to put extra configurations in place to support requirements like recoverability or data sovereignty that aren't available out-of-the-box with Log Analytics workspaces. In these cases, when practical, use native Azure or Microsoft tools to minimize the number of tools that your organization must support.
>
> - **Treat your workspaces as static rather than ephemeral components:** Like other types of data stores, workspaces shouldn't be considered among the ephemeral components of your workload. The Well-Architected Framework generally favors immutable infrastructure and the ability to quickly and easily replace resources within your workload as part of your deployments. But the loss of workspace data can be catastrophic and irreversible.
>
>    For this reason, leave workspaces out of deployment packages that replace infrastructure during updates, and only perform in-place upgrades on the workspaces.
>
> - **Ensure that the operations staff is trained on Kusto Query Language:** Train staff to create or modify queries when needed. If operators are unable to write or modify queries, it can slow critical troubleshooting or other functions because operators must rely on other teams to do that work for them.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Design the [Log Analytics workspace architecture](/azure/azure-monitor/logs/workspace-design) to meet your business requirements, including the number of workspaces to create and where to place them. <br><br> If your workload uses a centralized platform team offering, ensure that you set all necessary operational access. | A well-designed workspace strategy maximizes your workload's operational efficiency by limiting the distribution of your operational and security data, increasing visibility into potential problems, making patterns easier to identify, and minimizing your maintenance requirements. |
| Deploy Log Analytics workspaces by using IaC templates such as [ARM templates](/azure/azure-monitor/logs/quick-create-workspace), [Bicep](/azure/azure-resource-manager/bicep/overview), or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/log_analytics_workspace.html). Define workspace configurations and saved queries in version-controlled templates. <br><br> Parameterize templates for environment-specific settings while maintaining standardized baseline configurations. | IaC templates eliminate configuration drift between environments and reduce deployment errors through consistent, repeatable processes. <br><br> Version control enables change tracking and facilitates audit trails for compliance requirements. |
| Implement continuous integration and continuous delivery (CI/CD) pipelines that automate Log Analytics workspace deployments through [Azure Pipelines](/azure/azure-resource-manager/bicep/add-template-to-azure-pipelines) or [GitHub Actions](/devops/deliver/iac-github-actions). Integrate automated testing to validate workspace configurations before production deployment.<br><br> Colocate workspace infrastructure code with application code repositories to apply consistent [safe deployment practices](/azure/well-architected/operational-excellence/safe-deployments). | Automated CI/CD pipelines reduce deployment time while maintaining consistent quality through validation. <br><br> Safe deployment practices minimize risk of human error and provide rollback capabilities when problems occur during updates. |
| Enforce workspace configuration standards by using [Azure Policy](/azure/governance/policy/overview) with built-in policies for Log Analytics workspaces. Create custom policies for organization-specific requirements such as mandatory diagnostic settings and naming conventions. <br><br> Implement policy assignments at appropriate scopes to automatically apply governance rules to new workspaces and detect configuration drift. | Policy enforcement ensures consistent governance across all workspaces without manual oversight, which reduces operational overhead. Automated compliance checking prevents security and operational problems by detecting configuration drift. <br><br> Standardized configurations through policies support scalable workspace management and enable consistent audit posture. |
| Use [Log Analytics Workspace Insights](/azure/azure-monitor/logs/workspace-design) to track the health and performance of your Log Analytics workspaces. Review the information that Log Analytics Workspace Insights provides regularly to track the health and operation of each of your workspaces. <br><br> Create alert rules based on the [operation table](/azure/azure-monitor/logs/monitor-workspace) to be proactively notified when an operational problem occurs. Use [recommended alerts for the workspace](/azure/azure-monitor/logs/log-analytics-workspace-health) to simplify how you create the most critical alert rules. | Log Analytics Workspace Insights provides a unified view of the usage, performance, health, agents, queries, and change log for all your workspaces. <br><br> Log Analytics Workspace Insights enables the creation of easily understood visualizations like dashboards or reports that operations teams and stakeholders can use to track workspace health. |
| Practice continuous improvement by frequently revisiting Azure [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) on your resources, [DCRs](/azure/azure-monitor/agents/data-collection-rule-overview), and application log verbosity. Ensure that you optimize your log collection strategy through frequent reviews of your resource settings. <br><br> From an operational standpoint, look to reduce the noise in your logs by focusing on those logs that provide useful information about a resource's health status. | Continuous improvement practices help operators investigate and troubleshoot problems and handle routine, improvised, or emergency tasks as they occur. These practices also reduce log volume by focusing on activities that are most important for your operations team to track. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. This strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Log Analytics workspaces.

> [!div class="checklist"]
>
> - **Familiarize yourself with the fundamentals of [log data ingestion latency in Azure Monitor](/azure/azure-monitor/logs/data-ingestion-time):** There are several factors that contribute to latency when ingesting logs into your workspaces. Many of these factors are inherent to the Azure Monitor platform.
>
>    Understanding the factors and the normal latency behavior can help you set appropriate expectations within your workload operations teams.
>
> - **Separate your nonproduction and production workloads:** Production-specific workspaces mitigate any overhead that nonproduction systems might introduce. Separation reduces the overall footprint of your workspaces by requiring fewer resources to handle log data processing.
>
> - **Choose the right deployment regions to meet your performance requirements:** Deploy your Log Analytics workspace and DCEs close to your workload. Where you deploy your workload should inform your choice of the appropriate region in which to deploy your workspace and your DCEs.
>
>    You might need to weigh the performance benefits of deploying your workspaces and DCEs in the same region as your workload against your reliability requirements if you have already deployed your workload into a region that can't support those requirements for your log data.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Configure [log query auditing](/azure/azure-monitor/logs/query-audit) and use [Log Analytics Workspace Insights](/azure/azure-monitor/logs/log-analytics-workspace-insights-overview#query-audit-tab) to identify slow and inefficient queries. <br><br> Refer to [Optimize log queries in Azure Monitor](/azure/azure-monitor/logs/query-optimization) for guidance about how to improve performance of your slow log queries. | Optimized queries return results faster and use fewer resources on the back end, which also makes the processes that rely on those queries more efficient. |
| Use Search Jobs for complex analytical queries on large datasets and long-term retention data.<br><br> [Search Jobs](/azure/azure-monitor/logs/search-jobs) are asynchronous queries that run on any data in your Log Analytics workspace, including data from the long-term retention period. Search Jobs create new Analytics tables within your workspace that make results available for further queries. This capability enables separation of analytical workloads from operational monitoring, improving system performance while maintaining comprehensive data access. | Search Jobs support complex historical data analysis without impacting real-time monitoring performance. They enable dedicated analytical processing with minimal resource conflicts, allowing security teams and analysts to run intensive queries on archived data while preserving operational monitoring responsiveness. |
| Review [Azure Monitor service limits](/azure/azure-monitor/service-limits#logs-ingestion-api) and [Log Analytics workspaces](/azure/azure-monitor/service-limits#log-analytics-workspaces) limits to understand restrictions that might affect your performance and workspace design. <br><br> Design appropriately to mitigate service limits, which might require you to use multiple workspaces to avoid hitting limits associated with a single workspace. | Understanding the limits that might affect the performance of your workspace helps you design appropriately to mitigate them and balance design decisions against requirements and targets for other pillars. |
| Create [DCRs](/azure/azure-monitor/agents/data-collection-rule-overview) specific to data source types inside one or more defined observability scopes. <br><br> Create separate DCRs for performance and events to optimize the back-end processing compute usage. | Separate DCRs for performance and events help mitigate back-end resource exhaustion and prevent excessive compute resource consumption that might cause the Azure Monitor agent to become unresponsive. |

## Azure policies

Azure provides an extensive set of built-in policies related to Log Analytics and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether the following controls are in place:

- Log Analytics clusters are encrypted with customer-managed keys.

- Saved queries are stored in customer storage accounts for encryption.

- Log Analytics workspaces block non-Microsoft Entra ID-based ingestion.

- Log Analytics workspaces block log ingestion and querying from public networks.

- Private link configurations are properly implemented for secure access.

For comprehensive governance, review the [Azure Policy built-in definitions for Log Analytics](/azure/governance/policy/samples/built-in-policies#log-analytics) and other policies that might affect the security of the monitoring and logging infrastructure.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. 

For more information, see [Advisor](/azure/advisor).

## Next step

> [!div class="nextstepaction"]
> [Best practices for a complete deployment of Azure Monitor](/azure/cloud-adoption-framework/manage/monitor)

<!-- Updated: August 5, 2025 for Azure Update 498462, 498558 -->
