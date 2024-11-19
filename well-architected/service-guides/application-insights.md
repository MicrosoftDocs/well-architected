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

[Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extensible Application Performance Management (APM) service that is part of [Azure Monitor](/azure/azure-monitor/overview). It is designed for developers and DevOps professionals to monitor live applications and automatically detect performance anomalies. The application monitoring platform includes powerful analytics tools to help diagnose issues and understand how users interact with your application. For general monitoring principles, see [Monitoring and diagnostics guidance](/azure/architecture/best-practices/monitoring). This guide identifies the different types of data and outlines the required analysis that Azure Monitor supports. It also identifies the data collected by Application Insights that enables this analysis.

This article assumes that you understand system design principles. It provides architectural best practices for Application Insights based on the five pillars of the [Azure Well-Architected Framework](/azure/architecture/framework/): Reliability, Security, Cost Optimization, Operational Excellence, and Performance Efficiency.

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

> [!NOTE]
> Application Insights stores and exposes data in a [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview#overview-of-log-analytics-in-azure-monitor) workspace. Keep Log Analytics as a critical dependency on Application Insights when evaluating your metrics. For more information, see [Azure Well-Architected Framework perspective on Log Analytics](azure-log-analytics.md).

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

Use the following information to minimize the repercussions of a single component failing within Application Insights.

### Design checklist for Reliability

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the dependency on Log Analytics. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> * **Align your application monitoring design with business objectives and avoid unnecessary complexity.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need and where to deploy them.
>
> * **Configure sampling based on the criticality of the data.** Identify the user and system flows of your workload, determine the events you want to gather, and configure sampling accordingly.
>     * For manually instrumented applications, see [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-configuration?tabs=aspnetcore#enable-sampling) or [Application Insights SDKs (Classic API)](/azure/azure-monitor/app/sampling-classic-api).
>     * For autoinstrumentation, sampling is covered in environment-specific articles, for example [Configure sampling in Azure Functions](/azure/azure-functions/configure-monitoring?tabs=v2#configure-sampling).
>
> * **Create a comprehensive resiliency plan.**
>     * Start by conducting a Failure Mode Analysis (FMA) to identify potential scenarios where Application Insights might fail or become unreachable. This could be due to various reasons such as network issues, authentication problems, or service disruptions.
>     * Determine how your workload should behave if Application Insights is unreachable at boot or during runtime based on the criticality of application monitoring to your business goals. Define and document the expected behavior of your workload.
>     * Finally, test your resiliency plan. For example, you can validate network failures using an NSG (Network Security Group) rule, and authentication failures by changing the connection string.
>
> * **Plan for workspace resiliency and recovery by defining targets for your data collection components.**
>     * Assess the criticality of the data you're collecting and determine if it needs to be recoverable.
>     * Review service limits for [Application Insights](/azure/azure-monitor/service-limits#application-insights) and the underlying [Log Analytics workspace](/azure/azure-monitor/service-limits#log-analytics-workspaces) to understand restrictions on data collection and retention, and other aspects of the service.
>     * Where the availability of Application Insights telemetry within the retention period defined is business critical, consider moving to a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters?tabs=azure-portal) to take advantage of workspace resilience.
>     * Use [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) to export platform logs and metrics to the destination of your choice (for example, a storage account) for backup and recovery purposes.
>
> * **Implement a timely and reliable scaling strategy to plan for data ingestion growth.** Monitor and adjust limits on sampling and data ingestion as traffic grows to prevent data loss due to sampling or exceeding the [daily cap](/azure/azure-monitor/logs/daily-cap). This ensures that your data ingestion process scales effectively with increasing traffic.
>
> * **Ensure quick recovery of your application monitoring solution in case of service failure.** Adopt infrastructure as code and use [Bicep templates](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#azure-resource-manager-templates) to (re)create your user experience in Application Insights, including [alerts](/azure/azure-monitor/app/availability?tabs=standard#availability-alerts), [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard), and [queries](/azure/azure-monitor/logs/queries). This approach ensures that all critical components are quickly restored, minimizing downtime and maintaining service reliability.

### Recommendations for Reliability

| Recommendation | Benefits |
|----------------|----------|
| Use one Application Insights resource per workload per environment, such as one for development, one for staging, and one for production. | Using multiple Application Insights resources prevents mixing telemetry from different application versions and ensures a misconfiguration of one resource doesn't impact the logging for another environment. |
| Deploy your Application Insights resource in the same region as the underlying [Log Analytics workspace](/azure/azure-monitor/logs/log-analytics-workspace-overview). | Since Application Insights requires Log Analytics to function properly, having both resources in different regions doubles the chance for a regional failure to cause problems. |
| Implement a resilient workspace design by using [best practices for Azure Monitor Logs](/azure/azure-monitor/best-practices-logs). | Using best practices helps with continuous and robust monitoring by minimizing disruptions. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload. It is one of the most important aspects of any architecture. Azure Monitor provides features to employ both the principle of least privilege and defense in depth.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Application Insights. Use the following information to maximize the security of Application Insights and ensure that only authorized users access collected data.

### Design checklist for Security

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> * **Review the Azure Monitor [security baseline](/security/benchmark/azure/baselines/azure-monitor-security-baseline).** It provides guidance on security best practices to secure your cloud solutions on Azure, including Application Insights.
>
> * **Ensure your application monitoring solution is secure by keeping Application Insights instrumentation up-to-date.** Follow our [SDK update guidance](/azure/azure-monitor/app/sdk-support-guidance#sdk-update-guidance) and update Application Insights SDKs (Classic API) at least once a year. It's recommended to follow similar practices for the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable).
>
> * **Define a strategy for [handling personal data in Application Insights](/azure/azure-monitor/logs/personal-data-mgmt).** To ensure ongoing compliance, regularly verify that the collection and handling of data, including IP addresses and personal data, [comply with relevant regulations](/azure/compliance/) such as GDPR.
>
> * **Create intentional segmentation in your application monitoring design.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need.
>
> * **Enable identity and acecss management (IAM).** Use [managed identities](/entra/identity/managed-identities-azure-resources/overview) and [Microsoft Entra ID](/azure/azure-monitor/app/azure-ad-authentication?tabs=net) to ensure only authorized users can access your resources. This also eliminates the need for credentials management, as [Azure manages, rotates, and protects these credentials](/entra/architecture/service-accounts-managed-identities).
>
> * **Use [Azure Monitor customer-managed key](/azure/azure-monitor/logs/customer-managed-keys?tabs=portal).** By default, data in Azure Monitor is encrypted with Microsoft-managed keys. You can use your own encryption key to protect the data and saved queries in your workspaces. Customer-managed keys in Azure Monitor give you greater flexibility to manage access controls to stored data.
>
> * **Enhance data protection by securing storage systems and limiting access.** Visit the [Log Analytics service guide](azure-log-analytics.md#design-checklist-for-security) to learn about securing the data you're collecting.

### Recommendations for Security

| Recommendation | Benefit |
|----------------|---------|
| If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). | This approach eliminates the need for manual SDK updates, requires no code changes, and eliminates the overhead of maintaining instrumentation code. It can also enhance security by ensuring consistent application monitoring without manual intervention. |
| Stop collecting personal data or obfuscate, anonymize, or adjust collected data. Notice that Application Insights by default [doesn't store IP addresses](/azure/azure-monitor/app/ip-collection) and it's recommended to not change that. | Excludes your data from being considered *personal* and prevents breaking any compliance requirements or local regulations. |
| Use one Application Insights resource per workload per environment, such as one for development, one for staging, and one for production. | Using multiple Application Insights resources ensures data isolation and security, and helps in applying environment-specific configurations and access controls. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Insights and its environment.

For more information on how data charges are calculated for the underlying Log Analytics workspaces of your Application Insights resources, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/cost-usage).

### Design checklist for Cost Optimization

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md)
for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> * **Review [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/) to create a cost model.** Estimate the initial cost, run rates, and ongoing costs by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). Notice that Application Insights is billed through the Log Analytics workspace into which its log data ingested.
>
> * **Tune the amount of data collected.**
>     * Use [sampling](/azure/azure-monitor/app/sampling) on an Application Insights-level to reduce data traffic and storage costs while preserving a statistically correct analysis of application data.
>     * Application Insights has several possible [log sources](/azure/azure-monitor/app/app-insights-overview#logging-frameworks). Use log levels to tune and reduce trace log telemetry.
>
> * **[Set a daily cap](/azure/azure-monitor/logs/daily-cap) to limit unplanned charges for your workspace.** You can set a daily cap in both Application Insights and Log Analytics.
>
> * **Regularly review costs like regional pricing and available pricing tiers.** The most significant charges for most Azure Monitor implementations are typically ingestion and retention of data in your Log Analytics workspaces. For more information, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/logs/cost-logs) or visit the [Cost Optimization section](azure-log-analytics.md#design-checklist-for-cost-optimization) in the Log Analytics service guide.
>
> * **Regularly remove or optimize legacy, unneeded, and underutilized components of your application monitoring solution.** [Edit ApplicationInsights.config](/azure/azure-monitor/app/configuration-with-applicationinsights-config) to turn off collection modules that you don't need. For example, you might decide that performance counters or dependency data aren't required. Use telemetry filters or processors in code to help optimize component costs by not logging or sampling irrelevant calls.
>
> * **Optimize environment costs.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need and where to deploy them.  Optimize log levels and metrics collected per environment to manage costs effectively. For example, production environments might require different logging levels compared to development environments.
>
> * **Optimize flow costs.** Use telemetry filters or processors in code to help optimize component costs by not logging or sampling irrelevant calls.
>
> * **Optimize code costs.** Make sure to use updated Application Insights SDKs. Earlier versions of the ASP.NET Core SDK and Worker Service SDK [collect many counters by default](/azure/azure-monitor/app/eventcounters#default-counters-collected), which were collected as custom metrics. Use later versions to specify [only required counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).
>
> * **Optimize personnel time.** Utilize Application Insights experiences such as the [application map](/azure/azure-monitor/app/app-map) and [failure and performance views](/azure/azure-monitor/app/failures-and-performance-views), and customize saved [queries](/azure/azure-monitor/logs/queries), [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview) for your specific workload needs. Keep track of your deployments and other events with [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations).

### Recommendations for Cost Optimization

| Recommendation | Benefit |
|:---------------|:--------|
| [Set a daily cap in both Application Insights and Log Analytics](/azure/azure-monitor/logs/daily-cap#when-to-use-a-daily-cap), but try to avoid reaching it. | Reaching the daily cap can cause data to not be written in your Log Analytics workspace. Setting a cap is a risky but effective solution to throttle spend. |
| For standard availability tests, the need for testing from different locations might vary between production and pre-production environments. Reduce the amount of locations accordingly. | Tests incur costs, so have just enough tests to give you confidence in your workload's heath. |
| Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace. | This minimizes latency and reduce costs associated with cross-region telemetry. |
| Decrease the sample rate for less critical flows and increase it for flows with high criticality. Use telemetry filters for non-essential telemetry. | Data volume is a cost driver when designing your Application Insights integration. Reducing data volume through sample rates and filters are a potential solution to keeping your data collection under control. |
| [Limit the number of Ajax calls](/azure/azure-monitor/app/javascript-sdk-configuration) that can be reported in every page view or disable Ajax reporting. If you disable Ajax calls, you also disable [JavaScript correlation](/azure/azure-monitor/app/distributed-trace-data). | Data volume is a cost driver when designing your Application Insights integration for your client application. Reducing data volume through reduced client page reporting is a potential solution to keeping your data collection costs under control. |
| Use [preaggregated metrics](/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#preaggregated-metrics) to handle high-volume telemetry data more efficiently and limit the use of custom metrics. However, the Application Insights option to [enable alerting on custom metric dimensions](/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) can also increase costs. Measure to ensure cost savings are being obtained. | All custom metrics are stored in both logs and metric stores. Preaggregated metrics reduces storage costs related to custom metrics. |
| If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). | This approach optimizes software engineering time by eliminating the need for manual SDK updates, code changes related to new versions, and the overhead of maintaining instrumentation code. |
| Limit unwanted trace logging:<br><br>• Remove health checks, see *Filter out noisy traces* in [Optimizing Cost using the Azure Monitor OpenTelemetry Distro](https://techcommunity.microsoft.com/blog/azureobservabilityblog/optimizing-cost-using-the-azure-monitor-opentelemetry-distro/4115870).<br>• For Azure Kubernetes Service (AKS), adjust [control plane and data plane logs](/azure/aks/monitor-aks#azure-monitor-resource-logs).<br>• For Azure Functions, [adapt log levels and scope](/azure/azure-functions/configure-monitoring) to optimize log volume. | Limiting unwanted trace logging reduces the amount of stored data which can lower storage costs. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist for Operational Excellence

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Application Insights.

> [!div class="checklist"]
>
> * **Integrate your application monitoring team members' specializations into a robust set of practices to instrument and monitor your workload.** Chose an instrumentation method (i.e., autoinstrumentation or manual instrumentation) that is best for your situation based on your business needs and [Supported environments, languages, and resource providers](/azure/azure-monitor/app/codeless-overview#supported-environments-languages-and-resource-providers).
>
> * **Ensure optimal performance of your application monitoring solution by keeping Application Insights instrumentation up-to-date.** Follow our [SDK update guidance](/azure/azure-monitor/app/sdk-support-guidance#sdk-update-guidance) and update Application Insights SDKs (Classic API) at least once a year. It's recommended to follow similar practices for the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable). Using the latest SDK or Distro versions [ensures access to support services](/azure/azure-monitor/app/sdk-support-guidance) and provides the latest functionality with bug fixes.
>
> * **Formalize ideation and planning processes.** Use [work item ingetration](/azure/azure-monitor/app/release-and-work-item-insights?tabs=work-item-integration) to easily create work items in GitHub or Azure DevOps that have relevant Application Insights data embedded in them.
>
> * **Configure Application Insights to monitor the availability and responsiveness of your web application.** Use built-in features like [queries](/azure/azure-monitor/logs/queries) and [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard) based on your specific business needs. After you've deployed your application, set up recurring tests to monitor availability and responsiveness.
>
> * **Develop an effective emergency operations practice.** Use [alerts](/azure/azure-monitor/alerts/alerts-overview) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview) to identify and respond to incidents. Clearly define human responsiblities. For example, if your workload fails, determine who reboots the application.
>
> * **Clearly define your workload's safe deployment practices.** Use [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations) as part of your failure mitigation strategies to keep track of your deployments and other events.

### Recommendations for Operational Excellence

| Recommendation | Benefit |
|----------------|---------|
| If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). | This approach optimizes software engineering time by eliminating the need for manual SDK updates, code changes related to new versions, and the overhead of maintaining instrumentation code. |
| If your business needs require manual instrumentation, adopt the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable). | Avoid a future forced migration from the classic API SDKs by adopting an OpenTelemetry-based instrumentation method. |
| Use [connection strings](/azure/azure-monitor/app/migrate-from-instrumentation-keys-to-connection-strings). | Make telemetry ingestion more reliable and remove dependencies on global ingestion endpoints. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist for Performance Efficiency

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Application Insights.

> [!div class="checklist"]
>
> * **Define performance targets related to your application monitoring requirements.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need.
>
> * **Conduct capacity planning.** Understand usage patterns and how much data is coming in, and review ingestion and sample rates.
>
> * **Select the right region for your application monitoring solution.** Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace to prevent latency and reliability issues, see [Create a resource](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#create-a-workspace-based-resource).
>
> * **Evaluate [how many Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need.** Monitoring mulitple applications or application components with a single Application Insights resource provides a hollistic view, but can also impact the performance of experiences like [Application Map](/azure/azure-monitor/app/app-map?tabs=net) and [Usage](/azure/azure-monitor/app/usage?tabs=aspnetcore).
>
> * **Optimize code and infrastructure.** Regularly evaluate custom Application Insights code to reduce complexity, improve performance, and ensure that the code is up to date.
>
> * **Understand usage patterns and how much data is coming in by reviewing ingestion and sample rates.** To optimize data usage, adjust them accordingly and reduce the amount of [custom metrics](/azure/azure-monitor/app/api-custom-events-metrics), for example ITelemetryProcessor.
>
> * **Continuously optimize performance.** Use built-in features like [Smart Detection](/azure/azure-monitor/alerts/proactive-diagnostics), [queries](/azure/azure-monitor/logs/queries), and [dashboards](/azure/azure-monitor/app/overview-dashboard) to find components that show deteriorating performence.

### Recommendations for Performance Efficiency

| Recommendation | Benefit |
|:---------------|:--------|
| Use one Application Insights resource per workload per environment (such as one for development, one for staging, and one for production). | This prevents mixing telemetry from different application versions. |
| Where applicable, ensure that [profiling frequency and duration is set appropriately](/azure/azure-monitor/profiler/profiler-settings). | Avoid adding excessive overhead to the running process. |

## Azure policies

Azure provides built-in policies related to Application Insights and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

* [Application Insights components should block log ingestion and querying from public networks](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc02227-0cb6-4e11-8f53-eb0b22eab7e8)
* [Application Insights components should block non-Azure Active Directory based ingestion](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F199d5677-e4d9-4264-9465-efe1839c06bd)
* [Azure Monitor Logs for Application Insights should be linked to a Log Analytics workspace](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd550e854-df1a-4de9-bf44-cd894b39a95e)
* and more.

For comprehensive governance, review the [Azure Policy built-in definitions for Application Insights](/azure/governance/policy/samples/built-in-policies#monitoring) and other policies that might impact the security of the application performance monitoring solution.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Currently, Azure offers no Azure Advisor recommendations specific to Application Insights.

Here are some recommendations that can help you improve the cost effectiveness and operational excellence of Azure Monitor.

* [Cost Optimization](/azure/advisor/advisor-cost-recommendations#management-and-governance)
* [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#management-and-governance)

## Related resources

* [Azure Well-Architected Framework perspective on Log Analytics](./azure-log-analytics.md)

## Next steps

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.
<!--
* Use the following reference architectures as examples of how you can apply this article's guidance to a workload:

    * `reference-architecture`
    * `reference-architecture`
-->
Build implementation expertise by using the following product documentation:

* *Autoinstrumentation* (App Service) - [Enable application monitoring in Azure App Service for .NET, Node.js, Python, and Java applications](/azure/azure-monitor/app/codeless-app-service)
* *Manual instrumentation* (OpenTelemetry) - [Enable Azure Monitor OpenTelemetry for .NET, Node.js, Python, and Java applications](/azure/azure-monitor/app/opentelemetry-enable)
