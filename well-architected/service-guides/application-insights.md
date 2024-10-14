---
title: Azure Well-Architected Framework perspective on Azure Monitor Application Insights
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Application Insights.
author: kainawroth
ms.author: v-nawrothkai
ms.topic: conceptual
ms.subservice: waf-service-guide
products:
  - azure-application-insights
  - azure-log-analytics
  - azure-monitor
categories:
  - devops
  - management-and-governance
---

# Azure Well-Architected Framework perspective on Application Insights

## Overivew

[Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extensible Application Performance Management (APM) service that is part of [Azure Monitor](/azure/azure-monitor/overview). It is designed for developers and DevOps professionals to monitor live applications and automatically detect performance anomalies. It includes powerful analytics tools to help diagnose issues and understand how users interact with your application. For general monitoring principles, see [Monitoring and diagnostics guidance](/azure/architecture/best-practices/monitoring). This guide identifies the different types of data and outlines the required analysis that Azure Monitor supports. It also identifies the data collected by Application Insights that enables this analysis.

This article assumes that you understand system design principles. It provides architectural best practices for Application Insights based on the five pillars of the [Azure Well-Architected Framework](/azure/architecture/framework/): reliability, security, cost optimization, operational excellence, and performance efficiency.

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are *recommendations* on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Application Insights and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments.

### Technology scope

This guide focuses on the interrelated decisions for the following Azure resources:

* Application Insights
* Log Analytics workspaces

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

Use the following information to minimize the repercussions of a single component failing within Application Insights.

### Design checklist for Reliability

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the `offering-specific-aspects`. Extend the strategy to include more approaches
as needed.

Modern implementations of Application Insights store data in a [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview#overview-of-log-analytics-in-azure-monitor) workspace. Keep Log Analytics as a critical dependency on Application Insights when evaluating your metrics. For more information, see [Azure Well-Architected Framework perspective on Log Analytics](azure-log-analytics.md).

> [!div class="checklist"]
>
> * *RE:01 -* **Align your workload design with business objectives and avoid unnecessary complexity.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need and where to deploy them. Use one resource per workload per environment (such as one for staging and one for production) to prevent mixing telemetry from different application versions.<br><br>Deploy your Application Insights resources in the same region as the underlying Log Analytics workspace to minimize latency and reduce costs associated with cross-region telemetry. For more cost optimization strategies, see the [Cost Optimization pilar](#cost-optimization).<br><br>
>
> * *RE:02 -* **Configure sampling based on the criticality of the data.** Identify the user and system flows of your workload, determine the events you want to gather, and configure sampling accordingly. For highly critial data, adjust the sample rate to capture more data.<br><br>For instrumentation-specific instructions, see [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-configuration?tabs=aspnetcore#enable-sampling) or [Application Insights SDKs (Classic API)](/azure/azure-monitor/app/sampling-classic-api). Notice that sampling for autoinstrumentation is covered in environment-specific documentation, for example [Configure sampling in Azure Functions](/azure/azure-functions/configure-monitoring?tabs=v2#configure-sampling).<br><br>
>
> * *RE:03 -* **Conduct Failure Mode Analysis (FMA) to identify potential scenarios where Application Insights might fail or become unreachable, and create a resiliency plan.** This could occur due to various reasons such as network issues, authentication problems, or service disruptions.<br><br>Determine how your workload should behave if Application Inights is unreachable at boot or during runtime based on how critical application monitoring is to your business goals. Define and document the expected behavior of your workload.<br><br>
>
> * *RE:04 -* **Plan for workspace resilience and recovery by defining targets for your data collection components.** Assess the criticality of the data you're collecting and determine if it needs to be recoverable.<br><br>Review service limits for [Application Insights](/azure/azure-monitor/service-limits#application-insights) and the underlying [Log Analytics workspace](/azure/azure-monitor/service-limits#log-analytics-workspaces) to understand restrictions on data collection and retention, and other aspects of the service. Use [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) to export platform logs and metrics to the destination of your choice (e.g., a storage account) for backup and recovery purposes.<br><br>
>
> * *RE:06 -* **Implement a timely and reliable scaling strategy to plan for data ingestion growth.** Monitor and adjust limits on sampling and data ingestion as traffic grows to prevent data loss due to sampling or exceeding the [daily cap](/azure/azure-monitor/logs/daily-cap). This ensures that your data ingestion process scales effectively with increasing traffic.<br><br>
>
> * ~~*RE:07 -* **Strengthen the resiliency and recoverability of your workload.** Set up [availability tests](/azure/azure-monitor/app/availability) to reduce downtime by capturing your application becoming unavailable early. When using standard availability tests for your workloads, ensure you use multiple regions and *enable retries for availability test failures*.~~<br>
> **NOTE:** This is applicable to workloads, not specifically to Application Insights<br><br>
>
> * *RE:08 -* ~~**Test your resiliency plan.** For example, you can validate network failures by using an NSG (Network Security Group) rule, or authentication failures by changing the connection string.~~<br>
> **NOTE:** Can this be merged with *RE:03*?<br><br>
>
> * *RE:09 -* **Ensure quick recovery in case of service failure.** Use infrastructure as a service and leverage [ARM and Bicep templates](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#azure-resource-manager-templates) to (re)create the user experience in Application Insights, including alerts, [dashboard](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard), and [queries](/azure/azure-monitor/logs/queries). This approach ensures that all critical components are quickly restored, minimizing downtime and maintaining service reliability.

### Recommendations for Reliability

| Recommendation | Benefits |
|----------------|----------|

| *RE:01 -* Use [autoinstrumentation](/azure/azure-monitor/app/codeless-overview), if available. | Autoinstrumentation doesn't require any code changes and eliminates the overhead of maintaining instrumentation code. |
| *Other -* Implement a resilient workspace design by using [best practices for Azure Monitor Logs](/azure/azure-monitor/best-practices-logs). | This will ensure continuous and robust monitoring by minimizing disruptions. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload. It is one of the most important aspects of any architecture. Azure Monitor provides features to employ both the principle of least privilege and defense in depth.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Application Insights. Use the following information to maximize the security of Application Insights and ensure that only authorized users access collected data.

### Design checklist for Security

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> * *SE:01 -* Review the Azure Monitor [security baseline](/security/benchmark/azure/baselines/azure-monitor-security-baseline). This topic provides guidance on security best practices.
>
> * *SE:02 -* Keep Application Insights instrumentation up-to-date. Refer to our [SDK update guidance](/azure/azure-monitor/app/sdk-support-guidance#sdk-update-guidance) for the Application Insights SDK. While there's no such guidance for the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable), it's generally recommended to follow similar principles. Autoinstrumented applications don't require manual updates.
>
> * *SE:03 -* Define a strategy for [handling personal data in Azure Monitor Logs and Application Insights](/azure/azure-monitor/logs/personal-data-mgmt). Preferably, stop collecting personal data or obfuscate, anonymize, or adjust collected data to exclude it from being considered *personal*. This reduces the risk of handling personal data and ensures compliance with data protection regulations.
>
> * *SE:04 -* Determine the [appropriate number of Application Insights resources to deploy](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy).
>
> * *SE:05 -* Use [Microsoft Entra ID](/azure/azure-monitor/app/azure-ad-authentication?tabs=net) to enable identity and acecss management (IAM), and to ensure only authenticated telemetry is ingested in your Application Insights resources. Note that using managed identities is a key security practice.
>
> * *SE:06 -* Use [Azure Private Link](/azure/azure-monitor/logs/private-link-security) to ensure your monitoring data is only accessed through authorized private networks.
>
> * *SE:07 -* Use [Azure Monitor customer-managed key](/azure/azure-monitor/logs/customer-managed-keys?tabs=portal). Data in Azure Monitor is encrypted with Microsoft-managed keys. You can use your own encryption key to protect the data and saved queries in your workspaces. Customer-managed keys in Azure Monitor give you greater flexibility to manage access controls to logs.
>
> * *SE:09 -* Check the [Log Analytics WAF article](azure-log-analytics.md#design-checklist-for-security) to learn about securing the data you're collecting.

**Notes**

* *SE:10, SE:11, and SE:12 don't apply to Security in Application Insights.*
* *SE:08 is only part of Recommendations.*

### Recommendations for Security

| Recommendation | Benefit |
|----------------|---------|
| *SE:03 -* Application Insights by default [doesn't store IP addresses](/azure/azure-monitor/app/ip-collection). Our recommendation is not to change that. However, if you do need to collect and store IP addresses, verify the collection doesn't break any compliance requirements or local regulations. | This behavior is by design to help avoid unnecessary collection of personal data and IP address location information. |
| *SE:03 -* Regularly verify that the collection and handling of data, including IP addresses and personal data, [comply with relevant regulations](/azure/compliance/) such as GDPR. | Ensures ongoing compliance with data protection regulations and protects sensitive information. |
| *SE:03 - (from Copilot)* If personal data must be collected, build a process using the purge API path and the existing query API to meet obligations to export and delete any personal data associated with a user. | Ensures that personal data can be managed effectively and in compliance with data protection regulations. |
| *SE:04 -* Deploy separate Application Insights resources for different environments (e.g., development, testing, production). | This will ensure data isolation and security, and help in applying environment-specific configurations and access controls. |
| *SE:04 -* Group Application Insights resources by application or service to simplify management and monitoring. | This approach allows for more granular control over data collection and access permissions. |
| *SE:08 -* Use [autoinstrumentation](/azure/azure-monitor/app/codeless-overview), if available. | Autoinstrumentation doesn't require any code changes and eliminates the overhead of maintaining instrumentation code, which can improve security by ensuring that your application is consistently monitored without manual intervention. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Insights and its environment.

For more information on how data charges are calculated for the underlying Log Analytics workspaces of your Application Insights resources, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/cost-usage).

### Design checklist for Cost Optimization

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md)
for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> * *CO:02 -* Use [sampling](/azure/azure-monitor/app/sampling) to reduce data traffic, data, and storage costs, while preserving a statistically correct analysis of application data.
>
> * *CO:04 -* [Set the daily cap](/azure/azure-monitor/logs/daily-cap#set-the-daily-cap) to limit unplanned charges for your workspace. This can be used as a cost-control measure in Application Insights.
>
> * *CO:05 -* Regularly review cost savings like regional pricing, available pricing tiers, etc. For more information, see [Cost Optimization for Log Analytics](azure-log-analytics.md#design-checklist-for-cost-optimization).
>
> * *CO:06 -* The most significant charges for most Azure Monitor implementations are typically ingestion and retention of data in your Log Analytics workspaces. For more information, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/logs/cost-logs).
>
> * *CO:07 -* Optimize modules and AJAX calls. [Edit ApplicationInsights.config](/azure/azure-monitor/app/configuration-with-applicationinsights-config) to turn off collection modules that you don't need.
>
> * *CO:07 -* Limit the use of custom metrics. The Application Insights option to [Enable alerting on custom metric dimensions](/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) can increase costs. Using this option can result in the creation of more pre-aggregation metrics.
>
> * *CO:07 -* Use [preaggregated metrics](/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#preaggregated-metrics) which are designed to handle high-volume telemetry data more efficiently.
>
> * *CO:08 -* Keep Application Insights and your workloads in the same region. If your business-goals require Application Insights to be deployed in a different region, consider that sending telemetry across regions increases costs.
>
> * *CO:08 -* Optimize log levels per environment to manage costs effectively. For example,production environments might require different logging levels compared to development environments.
>
> * *CO:09 -* Use telemetry filters or processors in code to help optimize component costs by not logging or sampling irrelevant calls.
>
> * *CO:13 -* Optimize personnel time by utilizing (and customizing if available) Application Insights features like the failure and performance experiences, saved [queries](/azure/azure-monitor/logs/queries), [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard), [workbooks](/azure/azure-monitor/visualize/workbooks-overview), etc.
>
> * *CO:13 -* [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations) *TO DO*
>
> * *CO:14 -* Evaluate [how many Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need.

**Notes**

* *CO:01 and CO:03 don't apply to Cost Optimization in Application Insights.*
* *CO:10 and CO:12 refer to practices which have already been covered.*
* *CO:11 is only part of Recommendations.*

### Recommendations for Cost Optimization

| Recommendation | Benefit |
|:---------------|:--------|
| *CO:04 -* If a [daily cap](/azure/azure-monitor/logs/daily-cap) is set, try not to reach it since it can cause data to not be written in your Log Analytics workspace. *Note: If you have a workspace-based Application Insights (recommended), use the daily cap in workspace to limit ingestion and costs instead of using the cap in Application Insights.* | ... |
| *CO:07 -* If you put calls to `TrackMetric` in your application, you can reduce traffic by using the overload that accepts your calculation of the average and standard deviation of a batch of measurements. Alternatively, you can use a [pre-aggregating package](https://www.myget.org/gallery/applicationinsights-sdk-labs). | ... |
| *CO:07 -* For standard tests, the need for testing from different locations might vary between production and pre-production environments. Reduce the amount of locations accordingly. | Reducing the amount of locations will save costs. |
| *CO:09 -* Decrease the sample rate for less critical flows and increase it for flows with high criticality. Use telemetry filters for non-essential telemetry. | ... |
| *CO:11 -* [Limit the number of Ajax calls](/azure/azure-monitor/app/javascript-sdk-configuration) that can be reported in every page view or disable Ajax reporting. If you disable Ajax calls, you also disable [JavaScript correlation](/azure/azure-monitor/app/javascript-sdk-configuration). | ... |
| *CO:11 -* Ensure use of updated Application Insights SDKs. | Earlier versions of the ASP.NET Core SDK and Worker Service SDK [collect many counters by default](/azure/azure-monitor/app/eventcounters#default-counters-collected), which were collected as custom metrics. Use later versions to specify [only required counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected). |
| *CO:12 -* Use [autoinstrumentation](/azure/azure-monitor/app/codeless-overview), if available. | Autoinstrumentation doesn't require any code changes and eliminates the overhead of maintaining instrumentation code. |
| *Other -* Limit unwanted trace logging. | Application Insights has several possible [log sources](/azure/azure-monitor/app/app-insights-overview#logging-frameworks). Log levels can be used to tune and reduce trace log telemetry. Logging can also apply to the host. For example, customers using Azure Kubernetes Service (AKS) should adjust [control plane](/azure/aks/monitor-aks#aks-control-planeresource-logs) and [data plane logs](/azure/aks/monitor-aks#aks-data-planecontainer-insights-logs) and customers using Azure functions should [adapt log levels and scope](/azure/azure-functions/configure-monitoring) to optimize log volume and costs. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist for Operational Excellence

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Application Insights.

> [!div class="checklist"]
>
> * *OE:01 -* Evaluate [how many Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need.
>
> * *OE:01 -* Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace to prevent latency and reliability issues, see [Create a resource](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#create-a-workspace-based-resource).
>
> * *OE:01 -* Determine which instrumentation method (i.e., autoinstrumentation or manual instrumentation) is best for your situation based on [Supported environments, languages, and resource providers](/azure/azure-monitor/app/codeless-overview#supported-environments-languages-and-resource-providers).
>
> * *OE:01 -* Update Application Insights SDKs (Classic API) annually to the latest version. This will [ensure access to support services](/azure/azure-monitor/app/sdk-support-guidance#application-insights-sdk-support-guidance) and provide the latest functionality with bug fixes.
>
> * *OE:03 -* Use [work item ingetration](/azure/azure-monitor/app/release-and-work-item-insights?tabs=work-item-integration) to easily create work items in GitHub or Azure DevOps that have relevant Application Insights data embedded in them.
>
> * *OE:07 -* Use built-in Application Insights features like [queries](/azure/azure-monitor/logs/queries) and [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard) based on your specific business needs.
>
> * *OE:08 -* Use [alerts](/azure/azure-monitor/alerts/alerts-overview) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview) to identify and respond to incidents.
>
> * *OE:11 -* Use [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations) as part of your failure mitigation strategies to see where you deployed a new build or other significant events.
>
> * *Other -* Be aware that Application Insights can be used to monitor deployed sites and services on-premises (or on an Azure Virtual Machine (VM)).
>
> * *Other -* Evaluate Java codeless application monitoring for your Java-based application development stack.
>
> * *Other -* Record custom events and metrics from sites and services in Application Insights.
>
> * *Other -* Use Application Insights to ingest existing log traces from common libraries, such as `ILogger`, `Nlog`, and `log4Net`.
>
> * *Other -* Become familiar with the Application Insights quotas and limits.
>
> * *Other -* Review the need for custom analysis. Use Application Insights data with tools such as Azure Dashboards or Power BI.

### Recommendations for Operational Excellence

| Recommendation | Benefit |
|----------------|---------|
| *OE:01 -* Use [autoinstrumentation](/azure/azure-monitor/app/codeless-overview), if available. | Automatic instrumentation is configured optimally, requires no developer investment, and is always up to date. |
| *OE:01 -* Adopt the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable#enable-azure-monitor-opentelemetry-for-net-nodejs-python-and-java-applications). | Avoid a future forced migration from the classic API SDKs by adopting OpenTelemetry, the new standard for manual instrumentation. |
| *OE:01 -* Transition from instrumentation keys to [connection strings](/azure/azure-monitor/app/migrate-from-instrumentation-keys-to-connection-strings#migrate-from-application-insights-instrumentation-keys-to-connection-strings). | Make telemetry ingestion more reliable by using and removing dependencies on global ingestion endpoints. |
| *Other -* Configure Application Insights to monitor the availability and responsiveness of your web application. | After you've deployed your application, you can set up recurring tests to monitor availability and responsiveness. Application Insights sends web requests to your application at regular intervals from points around the world. It can alert you if your application isn't responding or if it responds too slowly. |
| *Other -* Evaluate Java codeless application monitoring for your Java-based application development stack. | Java codeless application monitoring is all about simplicity. There are no code changes. You can enable the Java agent through a couple of configuration changes. The Java agent works in any environment and allows you to monitor all your Java applications. No matter if you're running your Java apps on Virtual Machines, on-premises, in Azure Kubernetes Service (AKS), on Windows, or Linux, the Java `3.0` agent will monitor your app. |
| *Other -* Configure sampling in Application Insights. | Ingestion sampling operates at the point where the data from your web servers, browsers, and devices reaches the Application Insights service endpoints. Although it doesn't reduce the data sent from your app, it does reduce the amount processed, retained, and charged by Application Insights. Use this type of sampling if your app often goes above its monthly quota. Use ingestion sampling if you don't have access to the Software Development Kit (SDK)-based types of sampling. |
| *Other -* Record custom events and metrics from sites and services in Application Insights. | Use Application Insights to record domain-specific custom events and metrics from your site or service. For example: *number-of-active-baskets* or *product-lines-out-of-stock*. |
| *Other -* Use Application Insights to ingest existing log traces from common libraries, such as `ILogger`, `Nlog`, and `log4Net`. | If you're already using a logging framework such as `ILogger`, `Nlog`, `log4Net`, or `System.Diagnostics.Trace`, we recommend sending your diagnostic tracing logs to Application Insights. For Python applications, send diagnostic tracing logs using `AzureLogHandler` in OpenCensus Python for Azure Monitor. You can explore and search these logs, which are merged with the other log files from your application. Merging the log files allows you to identify traces associated with each user request and correlate them with other events and exception reports. |
| *Other -* Become familiar with the Application Insights quotas and limits. | This information can influence your sampling model and your strategy for separating Application Insights resources. |
| *Other -* Review the need for custom analysis. Use Application Insights data with tools such as Azure Dashboards or Power BI. | There are several available options to analyze your Application Insights data. For example, you can create a dashboard in the Azure portal that includes tiles visualizing data from multiple Azure resources across different resource groups and subscriptions. Alternatively, you can use Power BI to analyze data combined with data from other sources and share insights. |

**Notes**

* *OE:02, OE:04, OE:05, OE:06, OE:09, OE:10, and OE:12 don't apply to Operational Excellence in Application Insights.*
* *OE:# is only part of Recommendations.*

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist for Performance Efficiency

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Application Insights.

> [!div class="checklist"]
>
> * *PE:02 -* Understand how much data is coming in by reviewing ingestion and sample rates.
>
> * *PE:03 -* Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace to prevent latency and reliability issues, see [Create a resource](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#create-a-workspace-based-resource).
>
> * *PE:04 -* Evaluate [how many Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need. Monitoring mulitple applications or application components with a single Application Insights resource provides a hollistic view, but can also impact the performance of experiences like [Application Map](/azure/azure-monitor/app/app-map?tabs=net) and [Usage](/azure/azure-monitor/app/usage?tabs=aspnetcore).
>
> * *PE:08 -* Reduce the amount of [custom metrics](/azure/azure-monitor/app/api-custom-events-metrics), for example ITelemetryProcessor.
>
> * *PE:09 -* Use [userflows](/azure/azure-monitor/app/usage?tabs=aspnetcore#user-flows---analyze-user-navigation-patterns) to prioritize critical flows.
>
> * *PE:10 -* To optimize operational tasks, use [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations) for deployments and [custom events](/azure/azure-monitor/app/api-custom-events-metrics) for other actions.
>
> * *PE:11 -* Use [Live Metrics stream](/azure/azure-monitor/app/live-stream?tabs=otel) and [availability alerts](/azure/azure-monitor/app/availability?tabs=standard#availability-alerts) to respond to live performance issues.
>
> * *PE:12 -* Use [Smart Detection](/azure/azure-monitor/alerts/proactive-diagnostics), [queries](/azure/azure-monitor/logs/queries), and [dashboards](/azure/azure-monitor/app/overview-dashboard) to continuously optimize performance.
>
> * *Other -* Regularly evaluate custom Application Insights code to ensure that it is still needed, efficient, and up to date.
>
> * *Other -* Where applicable, ensure that [profiling frequency and duration is set appropriately](/azure/azure-monitor/profiler/profiler-settings) to avoid adding excessive overhead to the running process.

**Notes**

* *PE:01, PE:05, and PE:06 don't apply to Performance Efficiency in Application Insights.*
* *PE:07 is only part of Recommendations.*

### Recommendations for Performance Efficiency

| Recommendation | Benefit |
|:---------------|:--------|
| *PE:07 -* Use [autoinstrumentation](/azure/azure-monitor/app/codeless-overview), if available. | Automatic instrumentation is configured optimally, requires no developer investment, and is always up to date. |
| *Other -* Regularly evaluate custom Application Insights code. | Reduce complexity, improve performance, and ensure that the code is up to date. |
| *Other -* Manage package versions. See [here](/azure/azure-monitor/app/opentelemetry-overview?tabs=aspnetcore#instrumentation-options) for instrumentation options. | ... |

## Azure policies

Azure provides an extensive set of built-in policies related to Application Insights and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

* [Application Insights components should block log ingestion and querying from public networks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc02227-0cb6-4e11-8f53-eb0b22eab7e8)
* [Application Insights components should block non-Azure Active Directory based ingestion](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F199d5677-e4d9-4264-9465-efe1839c06bd)
* [Azure Monitor Logs for Application Insights should be linked to a Log Analytics workspace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd550e854-df1a-4de9-bf44-cd894b39a95e)
* and more.

For comprehensive governance, review the [Azure Policy built-in definitions for Application Insights](/azure/governance/policy/samples/built-in-policies#monitoring) and other policies that might impact the security of the application performance monitoring solution.

## Azure Advisor recommendations

Currently, Azure offers no Azure Advisor recommendations related to Application Insights.

<!--
Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of <Azure-offering>.

* [Reliability](/azure/advisor/advisor-high-availability-recommendations#<offering-or-infrastructure-area-anchor>)
* [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations#<offering-or-infrastructure-area-anchor>)
* [Cost Optimization](/azure/advisaor/advisor-cost-recommendations#<offering-or-infrastructure-area-anchor>)
* [Performance](/azure/advisor/advisor-reference-performance-recommendations#<offering-or-infrastructure-area-anchor>)
* [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#<offering-or-infrastructure-area-anchor>)
-->

## Related resources

* `resource`
* `resource`

## Next steps

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

* Use the following reference architectures as examples of how you can apply this article's guidance to a workload:

    * `reference-architecture`
    * `reference-architecture`

* Build implementation expertise by using the following product documentation:

    * `Azure-offering-documentation`
    * `Azure-offering-documentation`
