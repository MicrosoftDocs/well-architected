---
title: Health modeling and observability of mission-critical workloads on Azure
description: This design area will focus on the process to define a robust health model, mapping quantified application health states through observability and operational constructs to achieve operational maturity
author: calcof
ms.author: calcof
ms.date: 08/16/2022
ms.topic: conceptual
categories: 
  - storage
  - web
  - devops

---

# Health modeling and observability of mission-critical workloads on Azure

Health modeling and observability are essential concepts to maximize reliability, which focuses on robust and contextualized instrumentation and monitoring. These concepts provide critical insight into application health, promoting the swift identification and resolution of issues.

Most mission-critical applications are significant in terms of both scale and complexity and therefore generate high volumes of operational data, which makes it challenging to evaluate and determine optimal operational action. Health modeling ultimately strives to maximize observability by augmenting raw monitoring logs and metrics with key business requirements to quantify application health, driving automated evaluation of health states to achieve consistent and expedited operations.

This design area focuses on the process to define a robust health model, mapping quantified application health states through observability and operational constructs to achieve operational maturity.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)
>

There are three main levels of operational maturity when striving to maximize reliability.
1) *Detect* and respond to issues as they happen.
1) *Diagnose* issues that are occurring or have already occurred.
1) *Predict* and prevent issues before they take place.

## Video: Define a health model for your mission-critical workload

> [!VIDEO https://learn-video.azurefd.net/vod/player?id=fd8c4e50-9d7f-4df0-97cb-d0474b581398&embedUrl=/azure/well-architected/mission-critical/mission-critical-health-modeling]

## Layered application health

To build a health model, first define  application health in the context of key business requirements by quantifying ‘healthy’ and ‘unhealthy’ states in a layered and measurable format. Then, for each  application component, refine the definition in the context of a steady running state and aggregated according to the application user flows. Superimpose with key non-functional business requirements for performance and availability. Finally, aggregate the health states for each individual user flow to form an acceptable representation of the overall application health. Once established, these layered health definitions should be used to inform critical monitoring metrics across all system components and validate operational subsystem composition.

> [!IMPORTANT]
> When defining what 'unhealthy' states, represent for all levels of the application. It's important to distinguish between transient and non-transient failure states to qualify service degradation relative to unavailability.

### Design considerations

- The process of modeling health is a top-down design activity that starts with an architectural exercise to define all user flows and map dependencies between functional and logical components, thereby implicitly mapping dependencies between Azure resources.

- A health model is entirely dependent on the context of the solution it represents, and therefore can't be solved 'out-of-the-box' because 'one size doesn't fit all'.
  - Applications will differ in composition and dependencies
  - Metrics and metric thresholds for resources must also be finely tuned in terms of what values represent healthy and unhealthy states, which are heavily influenced by encompassed application functionality and target non-functional requirements.

- A layered health model enables application health to be traced back to lower level dependencies, which helps to quickly root cause service degradation.

- To capture health states for an individual component, that component's distinct operational characteristics must be understood under a steady state that is reflective of production load. Performance testing is therefore a key capability to define and continually evaluate application health.

- Failures within a cloud solution may not happen in isolation. An outage in a single component may lead to several capabilities or additional components becoming unavailable.
  - Such errors may not be immediately observable.

### Design recommendations

- Define a measurable health model as a priority to ensure a clear operational understanding of the entire application.
  - The health model should be layered and reflective of the application structure.
  - The foundational layer should consider individual application components, such as  Azure resources.
  - Foundational components should be aggregated alongside key non-functional requirements to build a business-contextualized lens into the health of system flows.
  - System flows should be aggregated with appropriate weights based on business criticality to build a meaningful definition of overall application health. Financially significant or customer-facing user flows should be prioritized.
  - Each layer of the health model should capture what ‘healthy’ and ‘unhealthy’ states represent.
  - Ensure the heath model can distinguish between transient and non-transient unhealthy states to isolate service degradation from unavailability.

- Represent health states using a granular health score for every distinct application component and every user flow by aggregating health scores for mapped dependent components, considering key non-functional requirements as coefficients.
  - The health score for a user flow should be represented by the lowest score across all mapped components, factoring in relative attainment against non-functional requirements for the user flow.
  - The model used to calculate health scores must consistently reflect operating health, and if not, the model should be adjusted and redeployed to reflect new learnings.
  - Define health score thresholds to reflect health status.

- The health score must be calculated automatically based on underlying metrics, which can be visualized through observability patterns and acted on through automated operational procedures.
  - The health score should become core to the monitoring solution, so that operating teams no longer have to interpret and map operational data to application health.

- Use the health model to calculate availability Service Level Objective (SLO) attainment instead of raw availability, ensuring the demarcation between service degradation and unavailability is reflected as separate SLOs.

- Use the health model within CI/CD pipelines and test cycles to validate application health is maintained after code and configuration updates.
  - The health model should be used to observe and validate health during load testing and chaos testing as part of CI/CD processes.

- Building and maintaining a health model is an iterative process and engineering investment should be aligned to drive continuous improvements.
  - Define a process to continually evaluate and fine-tune the accuracy of the model, and consider investing in machine learning models to further train the model.

### Example - Layered health model

This is a simplified representation of a layered application health model for illustrative purposes. A comprehensive and contextualized health model is provided in the Mission-Critical reference implementations:
- [Mission-Critical Online](https://github.com/Azure/Mission-Critical-Online)
- [Mission-Critical Connected](https://github.com/Azure/Mission-Critical-Connected)

When implementing a health model it's important to define the health of individual components through the aggregation and interpretation of key resource-level metrics. An example of how resource metrics can be used is the image below:

![Mission-critical example health definitions](./images/mission-critical-example-health-definitions.png "Mission-critical example health definitions")

This definition of health can subsequently be represented by a KQL query, as demonstrated by the example query below that aggregates InsightsMetrics (Container insights) and AzureMetrics (diagnostics setting for AKS cluster) and compares (inner join) against modeled health thresholds.

``` kql
// ClusterHealthStatus
let Thresholds=datatable(MetricName: string, YellowThreshold: double, RedThreshold: double) [
    // Disk Usage:
    "used_percent", 50, 80,
    // Average node cpu usage %:
    "node_cpu_usage_percentage", 60, 90,
    // Average node disk usage %:
    "node_disk_usage_percentage", 60, 80,
    // Average node memory usage %:
    "node_memory_rss_percentage", 60, 80
    ];
InsightsMetrics
| summarize arg_max(TimeGenerated, *) by Computer, Name
| project TimeGenerated,Computer, Namespace, MetricName = Name, Value=Val
| extend NodeName = extract("([a-z0-9-]*)(-)([a-z0-9]*)$", 3, Computer)
| union (
    AzureMetrics
    | extend ResourceType = extract("(PROVIDERS/MICROSOFT.)([A-Z]*/[A-Z]*)", 2, ResourceId)
    | where ResourceType == "CONTAINERSERVICE/MANAGEDCLUSTERS"
    | summarize arg_max(TimeGenerated, *) by MetricName
    | project TimeGenerated, MetricName, Namespace = "AzureMetrics", Value=Average
    )
| lookup kind=inner Thresholds on MetricName
| extend IsYellow = iff(Value > YellowThreshold and Value < RedThreshold, 1, 0)
| extend IsRed = iff(Value > RedThreshold, 1, 0)
| project NodeName, MetricName, Value, YellowThreshold, IsYellow, RedThreshold, IsRed
```

The resulting table output can subsequently be transformed into a health score for easier aggregation at higher levels of the health model.

```kql
// ClusterHealthScore
ClusterHealthStatus
| summarize YellowScore = max(IsYellow), RedScore = max(IsRed)
| extend HealthScore = 1-(YellowScore*0.25)-(RedScore*0.5)
```

These aggregated scores can subsequently be represented as a dependency chart using visualization tools such as Grafana to illustrate the health model. 

This image shows an example layered health model from the Azure Mission-Critical [online reference implementation](https://github.com/Azure/Mission-Critical-online), and demonstrates how a change in health state for a foundational component can have a cascading impact to user flows and overall application health (the example values correspond to the table in the previous image).

![Mission Critical Example Health Model Visualization](./images/mission-critical-example-fault-states.png "Mission Critical Example Health Model Visualization")

## Demo video: Monitoring and health modeling demo 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE55Nd9]

## Unified data sink for correlated analysis

Many operational datasets must be gathered from all system components to accurately represent a defined heath model, considering logs and metrics from both application components and underlying Azure resources. This vast amount of data ultimately needs to be stored in a format that allows for near-real time interpretation to facilitate swift operational action. Moreover, correlation across all encompassed data sets is required to ensure effective analysis is unbounded, allowing for the layered representation of health.

A unified data sink is required to ensure all operational data is swiftly stored and made available for correlated analysis to build a 'single pane' representation of application health. Azure provides several different operational technologies under the umbrella of [Azure Monitor](/azure/azure-monitor/overview#overview), and the Log Analytics workspace serves as the core Azure-native data sink to store and analyze operational data.

![Mission Critical Health Data Collection](./images/mission-critical-health-data-collection.png "Mission Critical Health Data Collection")

### Design considerations

**Azure Monitor**

- Azure Monitor is enabled by default for all Azure subscriptions, but Azure Monitor Logs (Log Analytics workspace) and Application Insights resources must be deployed and configured to incorporate data collection and querying capabilities.

- Azure Monitor supports three types of observability data: logs, metrics, and distributed traces.
  - Logs are stored in Log Analytics workspaces, which is based on [Azure Data Explorer](/azure/data-explorer/). Log queries are stored in query packs that can be shared across subscriptions, and are used to drive observability components such as dashboards, workbooks, or other reporting and visualization tools.
  - Metrics are stored in an internal time-series database. For most Azure resources, metrics are automatically collected and [retained](/azure/azure-monitor/essentials/data-platform-metrics#retention-of-metrics) for 93 days. Metric data can also be sent to the Log Analytics workspace using a [diagnostic setting](/azure/azure-monitor/essentials/diagnostic-settings) for the resource.

- All Azure resources expose logs and metrics, but resources must be appropriately configured to route diagnostic data to your desired data sink.

>[!TIP]
> Azure provides various [Built-In Policies](/azure/azure-monitor/policy-reference) that can be applied to ensure deployed resources are configured to send logs and metrics to a Log Analytics workspace.

- It's not uncommon for regulatory controls to require operational data remains within originating geographies or countries/regions.  Regulatory requirements may stipulate the retention of critical data types for an extended period of time. For example, in regulated banking, audit data must be retained for at least seven years.

- Different operational data types may require different retention periods. For example, security logs may need to be retained for a long period, while performance data is unlikely to require long-term retention outside the context of AIOps.

- Data can be [archived](/azure/azure-monitor/logs/data-retention-archive) or [exported](/azure/azure-monitor/logs/logs-data-export) from Log Analytics workspaces for long term retention and/or auditing purposes.

- [Dedicated Clusters](/azure/azure-monitor/logs/logs-dedicated-clusters) provide a deployment option that enables Availability Zones for protection from zonal failures in supported Azure regions. Dedicated Clusters require a minimum daily data ingest commitment.

- Log Analytics workspaces are deployed into a specified Azure region.

- To protect against loss of data from unavailability of a Log Analytics workspace, resources can be configured with multiple diagnostic settings. Each diagnostic setting can send metrics and logs to a separate Log Analytics workspace.
  - Data sent to each additional Log Analytics workspace will incur extra costs.
  - The redundant Log Analytics workspace can be deployed into the same Azure region, or into separate Azure regions for additional regional redundancy.
  - Sending logs and metrics from an Azure resource to a Log Analytics workspace in a different region will incur inter-region data egress costs.
  - Some Azure resources require a Log Analytics workspace within the same region as the resource itself.
  - See [Best practices for Azure Monitor Logs](/azure/azure-monitor/best-practices-logs#reliability) for further availability options for the Log Analytics workspace.

- Log Analytics workspace data [can be exported to Azure Storage or Azure Event Hubs on a continuous, scheduled, or one-time basis](/azure/azure-monitor/logs/logs-data-export).
  - Data export allows for long-term data archiving and protects against possible operational data loss due to unavailability.
  - Available export destinations are Azure Storage or Azure Event Hubs. Azure Storage can be configured for different [redundancy levels](/azure/storage/common/storage-redundancy) including zonal or regional. Data export to Azure Storage stores the data within .json files.
  - Data export destinations must be within the same Azure region as the Log Analytics workspace. An event hub data export destination to be within the same region as the Log Analytics workspace. Azure Event Hubs geo-disaster recovery isn't applicable for this scenario.
  - There are several [data export limitations](/azure/azure-monitor/logs/logs-data-export#limitations). Only specific [tables in the workspace are supported](/azure/azure-monitor/logs/logs-data-export#supported-tables) for data export.
  - [Archiving](/azure/azure-monitor/logs/data-retention-archive) can be used to store data in a Log Analytics workspace for long-term retention at a reduced cost without exporting it.

- Azure Monitor Logs has [user query throttling limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#user-query-throttling), which may appear as reduced availability to clients, such as observability dashboards.
  - Five concurrent queries per user: if five queries are already running, additional queries are placed in a per-user concurrency queue until a running query ends.
  - Time in concurrency queue: if a query sits in the concurrency queue for over three minutes, it will be terminated and a 429 error code returned.
  - Concurrency queue depth limit: the concurrency queue is limited to 200 queries, and additional queries will be rejected with a 429 error code.
  - Query rate limit: there's a per-user limit of 200 queries per 30 seconds across all workspaces.

- [Query packs](/azure/azure-monitor/logs/query-packs) are Azure Resource Manager resources, which can be used to protect and recover log queries if the Log Analytics workspace is unavailable.
  - Query packs contain queries as JSON and can be stored external to Azure similar to other infrastructure-as-code assets.
    - Deployable through the Microsoft.Insights REST API.
    - If a Log Analytics workspace must be re-created the query pack can be redeployed from an externally stored definition.

- Application Insights can be deployed in a workspace-based deployment model, underpinned by a Log Analytics workspace where all the data is stored.

- [Sampling](/azure/azure-monitor/app/sampling) can be enabled within Application Insights to reduce the amount of telemetry sent and optimize data ingest costs.

- All data collected by Azure Monitor, including Application Insights, is [charged](https://azure.microsoft.com/pricing/details/monitor/) based on the volume of data ingested and the duration that data is retained.
  - Data ingested into a Log Analytics workspace can be retained at no additional charge up to first 31 days (90 days if Sentinel is enabled)
  - Data ingested into a workspace-based Application Insights is retained for the first 90 days at no extra charge.

- The [Log Analytics commitment tier pricing model](/azure/azure-monitor/logs/cost-logs#commitment-tiers) provides a reduced cost and a predictable approach to data ingest charges.
  - Any usage above the reservation level is billed at the same price as the current tier.

- Log Analytics, Application Insights, and Azure Data Explorer use the Kusto Query Language (KQL).

- Log Analytics queries are saved as *functions* within the Log Analytics workspace (`savedSearches`).

### Design recommendations

- Use Log Analytics workspace as a unified data sink to provide a 'single pane' across all operational data sets.
  - Decentralize Log Analytics workspaces across all used deployment regions. Each Azure region with an application deployment should consider a Log Analytics workspace to gather all operational data originating from that region. All global resources should use a separate dedicated Log Analytics workspace, which should be deployed within a primary deployment region.
    - Sending all operational data to a single Log Analytics workspace would create a single point of failure.
    - Requirements for data residency might prohibit data leaving the originating region, and federated workspaces solves for this requirement by default.
    - There's a substantial egress cost associated with transferring logs and metrics across regions.
  - All deployment stamps within the same region can use the same regional Log Analytics workspace.

- Consider configuring resources with multiple diagnostic settings pointing to different Log Analytics workspaces to protect against Azure Monitor unavailability for applications with fewer regional deployment stamps.

- Use Application Insights as a consistent Application Performance Monitoring (APM) tool across all application components to collect application logs, metrics, and traces.
  - Deploy Application Insights in a workspace-based configuration to ensure each regional Log Analytics workspace contains logs and metrics from both application components and underlying Azure resources.

- Use [cross-Workspace queries](/azure/azure-monitor/logs/cross-workspace-query) to maintain a unified 'single pane' across the different workspaces.

- Use [query packs](/azure/azure-monitor/logs/query-packs) to protect log queries in the event of workspace unavailability.
  - Store query packs within the application git repository as infrastructure-as-code assets.

- All Log Analytics workspaces should be treated as long-running resources with a different life-cycle to application resources within a regional deployment stamp.

- Export critical operational data from Log Analytics workspace for long-term retention and analytics to facilitate AIOps and advanced analytics to refine the underlying health model and inform predictive action.

- Carefully evaluate which data store should be used for long-term retention; not all data has to be stored in a hot and queryable data store.
  - It's strongly recommended to use Azure Storage in a GRS configuration for long-term operational data storage.
    - Use the Log Analytics workspace export capability to export all available data sources to Azure Storage.

- Select appropriate retention periods for operational data types within log analytics, configuring longer retention periods within the workspace where 'hot' observability requirements exist.

- Use Azure Policy to ensure all regional resources route operational data to the correct Log Analytics workspace.

>[!NOTE]
> When deploying into an Azure landing zone,  if there's a requirement for centralized storage of operational data, you can [fork](/azure/azure-monitor/logs/logs-data-export?tabs=portal) data at instantiation so it's ingested into both centralized tooling and Log Analytics workspaces dedicated to the application. Alternatively, expose access to application Log Analytics workspaces so that central teams can query application data. It's ultimately critical that operational data originating from the solution is available within Log Analytics workspaces dedicated to the application.
>
> If SIEM integration is required, do not send raw log entries, but instead send critical alerts.

- Only configure sampling within Application Insights if it's required to optimize performance, or if not sampling becomes cost prohibitive.
  - Excessive sampling can lead to missed or inaccurate operational signals.

- Use correlation IDs for all trace events and log messages to tie them to a given request.
  - Return correlation IDs to the caller for all calls not just failed requests.

- Ensure application code incorporates proper instrumentation and logging to inform the health model and facilitate subsequent troubleshooting or root cause analysis when required.
  - Application code should use Application Insights to facilitate [Distributed Tracing](/dotnet/core/diagnostics/distributed-tracing-concepts), by providing the caller with a comprehensive error message that includes a correlation ID when a failure occurs.

- Use [structured logging](https://stackify.com/what-is-structured-logging-and-why-developers-need-it/) for all log messages.

- Add meaningful health probes to all application components.
  - When using AKS, configure the health endpoints for each deployment (pod) so that Kubernetes can correctly determine when a pod is healthy or unhealthy.
  - When using Azure App Service, configure the [Health Checks](/azure/app-service/monitor-instances-health-check) so that scale out operations will not cause errors by sending traffic to instances that are not-yet ready, and making sure unhealthy instances are recycled quickly.

> If the application is subscribed to Microsoft Mission-Critical Support, consider exposing key health probes to Microsoft Support, so application health can be modelled more accurately by Microsoft Support.

- Log successful health check requests, unless increased data volumes can't be tolerated in the context of application performance, since they provide additional insights for analytical modeling.

- Do not configure production Log Analytics workspaces to apply a [daily cap](/azure/azure-monitor/logs/daily-cap), which limits the daily ingestion of operational data, since this can lead to the loss of critical operational data.
  - In lower environments, such as Development and Test, a daily cap can be considered as an optional cost saving mechanism.

- Provided operational data ingest volumes meet the minimum tier threshold, configure Log Analytics workspaces to use commitment tier based pricing to drive cost efficiencies relative to the 'pay-as-you-go' pricing model.

- It's strongly recommended to store Log Analytics queries using source control and use CI/CD automation to deploy them to relevant Log Analytics workspace instances.

## Visualization

Visually representing the health model with critical operational data is essential to achieve effective operations and maximize reliability. Dashboards should ultimately be utilized to provide near-real time insights into application health for DevOps teams, facilitating the swift diagnosis of deviations from steady state.

Microsoft provides several data visualization technologies, including Azure Dashboards, Power BI, and Azure Managed Grafana (currently in-preview). Azure Dashboards is positioned to provide a tightly integrated out-of-the-box visualization solution for operational data within Azure Monitor. It therefore has a fundamental role to play in the visual representation of operational data and application health for a mission-critical workload. However, there are several limitations in terms of the positioning of Azure Dashboards as a holistic observability platform, and as a result consideration should be given to the supplemental use of market-leading observability solutions, such as Grafana, which is also provided as a managed solution within Azure.

This section focuses on the use of Azure Dashboards and Grafana to build a robust dashboarding experience capable of providing technical and business lenses into application health, enabling DevOps teams and effective operation. Robust dashboarding is essential to diagnose issues that have already occurred, and support operational teams in detecting and responding to issues as they happen.

### Design considerations

- When visualizing the health model using log queries, note that there are [Log Analytics limits on concurrent and queued queries, as well as the overall query rate](/azure/azure-monitor/service-limits#user-query-throttling), with subsequent queries queued and throttled.

- Queries to retrieve operational data used to calculate and represent health scores can be written and executed in either Log Analytics or Azure Data Explorer.
  - Sample queries are available [here](https://github.com/Azure/Mission-Critical-Online/tree/main/src/infra/monitoring/queries).

- Log Analytics imposes several [query limits](/azure/azure-monitor/service-limits#user-query-throttling), which must be designed for when designing operational dashboards.

- The visualization of raw resource metrics, such as CPU utilization or network throughput, requires manual evaluation by operations teams to determine health status impacts, and this can be challenging during an active incident.

- If multiple users use dashboards within a tool like Grafana, the number of queries sent to Log Analytics multiplies quickly. 
  - Reaching the concurrent query limit on Log Analytics will queue subsequent queries, making the dashboard experience feel 'slow'. 

### Design Recommendations

- Collect and present queried outputs from all regional Log Analytics Workspaces and the global Log Analytics Workspace to build a unified view of application health.

>[!NOTE]
> If deploying into an Azure landing zone, consider querying the [central platform Log Analytics workspace](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring#plan-platform-management-and-monitoring) if key dependencies on platform resources exist, such as ExpressRoute for on-premises communication.

- A ‘traffic light’ model should be used to visually represent 'healthy' and 'unhealthy' states, with green used to illustrate when key non-functional requirements are fully satisfied and resources are optimally utilized. Use "Green", "Amber, and "Red" to represent "Healthy", "Degraded", and "Unavailable" states.

- Use Azure Dashboards to create operational lenses for global resources and regional deployment stamps, representing key metrics such as request count for Azure Front Door, server side latency for Azure Cosmos DB, incoming/outgoing messages for Event Hubs, and CPU utilization or deployment statuses for AKS.  Dashboards should be tailored to drive operational effectiveness, infusing learnings from failure scenarios to ensure DevOps teams have direct visibility into key metrics.

- If Azure Dashboards can't be used to accurately represent the health model and requisite business requirements, then it's strongly recommended to consider Grafana as an alternative visualization solution, providing market-leading capabilities and an extensive open-source plugin ecosystem. Evaluate the managed Grafana preview offering to avoid the operational complexities of managing Grafana infrastructure.

- When deploying self-hosted Grafana, employ a highly available and geo-distributed design to ensure critical operational dashboards can be resilient to regional platform failures and cascading error scenarios. 
  - Separate configuration state into an external datastore, such as Azure Database for Postgres or MySQL, to ensure Grafana application nodes remain stateless.
    - Configure database replication across deployment regions.
  - Deploy Grafana nodes to App Services in a highly available configuration across ones within a region, using container based deployments.
    - Deploy App Service instances across considered deployment regions.

    > App Services provides a low-friction container platform, which is ideal for low-scale scenarios such as operational dashboards, and isolating Grafana from AKS provides a clear separation of concern between the primary application platform and operational representations for that platform. Please refer to the Application Platform deign area for further configuration recommendations.

  - Use Azure Storage in a GRS configuration to host and manage custom visuals and plugins.
  - Deploy app service and database read-replica Grafana components to a minimum of two deployment regions, and consider employing a model where Grafana is deployed to all considered deployment regions.

> For scenarios targeting a >= 99.99% SLO, Grafana should be deployed within a minimum of 3 deployment regions to maximize overall reliability for key operational dashboards.

- Mitigate Log Analytics query limits by aggregating queries into a single or small number of queries, such as by using the KQL 'union' operator, and set an appropriate refresh rate on the dashboard.
  - An appropriate maximum refresh rate will depend on the number and complexity of dashboard queries; analysis of implemented queries is required.
  
- If the concurrent query limit of Log Analytics is being reached, consider optimizing the retrieval pattern by (temporarily) storing the data required for the dashboard in a high performance datastore such as Azure SQL.

## Automated incident response

While the visual representations of application health provide invaluable operational and business insights to support issue detection and diagnosis, it relies on the readiness and interpretations of operational teams, as well as the effectiveness of subsequent human-triggered responses. Therefore, to maximize reliability it's necessary to implement extensive alerting to detect proactively and respond to issues in near real-time.

[Azure Monitor](/azure/azure-monitor/alerts/alerts-overview) provides an extensive alerting framework to detect, categorize, and respond to operational signals through [Action Groups](/azure/azure-monitor/alerts/action-groups). This section will therefore focus on the use of Azure Monitor alerts to drive automated actions in response to current or potential deviations from a healthy application state.

>[!IMPORTANT]
>Alerting and automated action is critical to effectively detect and swiftly respond to issues as they happen, before greater negative impact can occur. Alerting also provides a mechanism to interpret incoming signals and respond to prevent issues before they occur.

### Design considerations

- Alert rules are defined to fire when a conditional criteria is satisfied for incoming signals, which can include various [data sources](/azure/azure-monitor/data-sources), such as metrics, log queries, or availability tests.

- Alerts can be defined within Log Analytics or Azure Monitor on the specific resource.

- Some metrics are only interrogatable within Azure Monitor, since not all diagnostic data points are made available within Log Analytics.

- The Azure Monitor Alerts API can be used to retrieve active and historic alerts.

- There are subscription limits related to alerting and action groups, which must be designed for:
  - [Limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#alerts) exist for the number of configurable alert rules.
  - The Alerts API has [throttling limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#alerts-api), which should be considered for extreme usage scenarios.
  - Action Groups have [several hard limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#action-groups) for the number of configurable responses, which must be designed for.
    - Each response type has a limit of 10 actions, apart from email, which has a limit of 1,000 actions.

- Alerts can be integrated within a layered health model by creating an Alert Rule for a saved log search query from the model's 'root' scoring function. For example, using 'WebsiteHealthScore' and alerting on a numeric value that represents an 'Unhealthy' state.

### Design recommendations

- For resource-centric alerting, create alert rules within Azure Monitor to ensure all diagnostic data is available for the alert rule criteria.

- Consolidate automated actions within a minimal number of Action Groups, aligned with service teams to support a DevOps approach.

- Respond to excessive resource utilization signals through automated scale operations, using Azure-native auto-scale capabilities where possible. Where built-in auto-scale functionality isn't applicable, use the component health score to model signals and determine when to respond with automated scale operations. Ensure automated scale operations are defined according to a capacity model, which quantifies scale relationships between components, so that scale responses encompass components that need to be scaled in relation to other components.

- Model actions to accommodate a prioritized ordering, which should be determined by business impact.

- Use the Azure Monitor Alerts API to gather historic alerts to incorporate within 'cold' operational storage for advanced analytics.

- For critical failure scenarios, which can't be met with an automated response, ensure operational 'runbook automation' is in-place to drive swift and consistent action once manual interpretation and sign out is provided. Use alert notifications to drive swift identification of issues requiring manual interpretation
  
- Create allowances within engineering sprints to drive incremental improvements in alerting to ensure new failure scenarios that haven't previously been considered can be fully accommodated within new automated actions.

- Conduct operational readiness tests as part of CI/CD processes to validate key alert rules for deployment updates.

## Predictive action and AI operations (AIOps)

Machine learning models can be applied to correlate and prioritize operational data, helping to gather critical insights related to filtering excessive alert 'noise' and predicting issues before they cause impact, as well as accelerating incident response when they do.

More specifically, an AIOps methodology can be applied to critical insights about the behavior of the system, users, and DevOps processes. These insights can include identifying a problem happening now (*detect*), quantifying why the problem is happening (*diagnose*), or signaling what will happen in the future (*predict*). Such insights can be used to drive actions that adjust and optimize the application to mitigate active or potential issues, using key business metrics, system quality metrics, and DevOps productivity metrics, to prioritize according to business impact. Conducted actions can themselves be infused into the system through a feedback loop that further trains the underlying model to drive additional efficiencies.

![Mission Critical AIOps Methodologies](./images/mission-critical-aiops-methodology.png "Mission Critical AIOps Methodologies")

There are multiple analytical technologies within Azure, such as Azure Synapse and Azure Databricks, which can be used to build and train analytical models for AIOps. This section will therefore focus on how these technologies can be positioned within an application design to accommodate AIOps and drive predictive action, focusing on Azure Synapse that reduces friction by bringing together the best of Azure's data services along with powerful new features.

AIOps is used to drive predictive action, interpreting and correlating complex operational signals observed over a sustained period in order to better respond to and prevent issues before they occur.

### Design considerations

- Azure Synapse Analytics offers multiple Machine Learning (ML) capabilities.
  - ML models can be trained and run on Synapse Spark Pools with libraries including MLLib, SparkML and MMLSpark, as well as popular open-source libraries, such as [Scikit Learn](https://scikit-learn.org/stable/).
  - ML models can be trained with common data science tools like PySpark/Python, Scala, or .NET.

- Synapse Analytics is integrated with Azure ML through Azure Synapse Notebooks, which enables ML models to be trained in an Azure ML Workspace using [Automated ML](/azure/machine-learning/concept-automated-ml).

- Synapse Analytics also enables ML capabilities using [Azure Cognitive Services](/azure/cognitive-services/what-are-cognitive-services) to solve general problems in various domains, such as [Anomaly Detection](/azure/cognitive-services/anomaly-detector/). Cognitive Services can be used in Azure Synapse, Azure Databricks, and via SDKs and REST APIs in client applications.

- Azure Synapse natively integrates with [Azure Data Factory](/azure/data-factory/introduction) tools to extract, transform, and load (ETL) or ingest data within orchestration pipelines.

- Azure Synapse enables external dataset registration to data stored in Azure Blob storage or Azure Data Lake Storage.
  - Registered datasets can be used in Synapse Spark pool data analytics tasks.

- Azure Databricks can be integrated into Azure Synapse Analytics pipelines for additional Spark capabilities.
  - Synapse orchestrates reading data and sending it to a Databricks cluster, where it can be transformed and prepared for ML model training.

- Source data typically needs to be prepared for analytics and ML.
  - Synapse offers various tools to assist with data preparation, including Apache Spark, Synapse Notebooks, and serverless SQL pools with T-SQL and built-in visualizations.

- ML models that have been trained, operationalized, and deployed can be used for _batch_ scoring in Synapse.
  - AIOps scenarios, such as running regression or degradation predictions in CI/CD pipelined, may require _real-time_ scoring.

- There are subscription limits for [Azure Synapse](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-synapse-analytics-limits), which should be fully understood in the context of an AIOps methodology.

- To fully incorporate AIOps it's necessary to feed near real-time observability data into real-time ML inference models on an ongoing basis.
  - Capabilities such as anomaly detection should be evaluated within the observability data stream.

### Design recommendations

- Ensure all Azure resources and application components are fully instrumented so that a complete operational dataset is available for AIOps model training.

- Ingest Log Analytics operational data from the global and regional Azure Storage Accounts into Azure Synapse for analysis.

- Use the Azure Monitor Alerts API to retrieve historic alerts and store it within cold storage for operational data to subsequently use within ML models. If Log Analytics data export is used, store historic alerts data in the same Azure Storage accounts as the exported Log Analytics data.

- After ingested data is prepared for ML training, write it back out to Azure Storage so that it's available for ML model training without requiring Synapse data preparation compute resources to be running.

- Ensure ML model operationalization supports both batch and real-time scoring.

- As AIOps models are created, implement MLOps and apply DevOps practices to [automate the ML lifecycle](/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle) for training, operationalization, scoring, and continuous improvement. Create an iterative CI/CD process for AIOps ML models.

- Evaluate [Azure Cognitive Services](/azure/cognitive-services/what-are-cognitive-services) for specific predictive scenarios due to their low administrative and integration overhead.  Consider [Anomaly Detection](/azure/cognitive-services/anomaly-detector/) to quickly flag unexpected variances in observability data streams.

## Next step

Review the deployment and testing considerations.

> [!div class="nextstepaction"]
> [Deployment and testing](mission-critical-deployment-testing.md)
