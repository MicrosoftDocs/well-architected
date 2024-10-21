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

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the `offering-specific-aspects`. Extend the strategy to include more approaches as needed.

Modern implementations of Application Insights store data in a [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview#overview-of-log-analytics-in-azure-monitor) workspace. Keep Log Analytics as a critical dependency on Application Insights when evaluating your metrics. For more information, see [Azure Well-Architected Framework perspective on Log Analytics](azure-log-analytics.md).

> [!div class="checklist"]
>
> * *RE:01 -* **Align your application monitoring design with business objectives and avoid unnecessary complexity.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need and where to deploy them. Use one resource per workload per environment (such as one for staging and one for production) to prevent mixing telemetry from different application versions.<br><br>Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace to minimize latency and reduce costs associated with cross-region telemetry. For more cost optimization strategies, go to the [Cost Optimization](#cost-optimization) section.<br><br>
>
> * *RE:02 -* **Configure sampling based on the criticality of the data.** Identify the user and system flows of your workload, determine the events you want to gather, and configure sampling accordingly. For highly critial data, adjust the sample rate to capture more data.<br><br>For instrumentation-specific instructions, see [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-configuration?tabs=aspnetcore#enable-sampling) or [Application Insights SDKs (Classic API)](/azure/azure-monitor/app/sampling-classic-api). For autoinstrumentation, sampling is covered in environment-specific documentation, for example [Configure sampling in Azure Functions](/azure/azure-functions/configure-monitoring?tabs=v2#configure-sampling).<br><br>
>
> * *RE:03 & RE:08 -* **Create a comprehensive resiliency plan.** Start by conducting a Failure Mode Analysis (FMA) to identify potential scenarios where Application Insights might fail or become unreachable. This could be due to various reasons such as network issues, authentication problems, or service disruptions.<br><br>Determine how your workload should behave if Application Insights is unreachable at boot or during runtime based on the criticality of application monitoring to your business goals. Define and document the expected behavior of your workload.<br><br>Finally, test your resiliency plan. For example, validate network failures using an NSG (Network Security Group) rule, and authentication failures by changing the connection string.<br><br>
>
> * *RE:04 -* **Plan for workspace resilience and recovery by defining targets for your data collection components.** Assess the criticality of the data you're collecting and determine if it needs to be recoverable.<br><br>Review service limits for [Application Insights](/azure/azure-monitor/service-limits#application-insights) and the underlying [Log Analytics workspace](/azure/azure-monitor/service-limits#log-analytics-workspaces) to understand restrictions on data collection and retention, and other aspects of the service. Use [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) to export platform logs and metrics to the destination of your choice (for example, a storage account) for backup and recovery purposes.<br><br>
>
> * *RE:06 -* **Implement a timely and reliable scaling strategy to plan for data ingestion growth.** Monitor and adjust limits on sampling and data ingestion as traffic grows to prevent data loss due to sampling or exceeding the [daily cap](/azure/azure-monitor/logs/daily-cap). This ensures that your data ingestion process scales effectively with increasing traffic.<br><br>
>
> * *RE:09 -* **Ensure quick recovery of your application monitoring solution in case of service failure.** Use infrastructure as code and leverage [ARM and Bicep templates](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#azure-resource-manager-templates) to (re)create your user experience in Application Insights, including [alerts](/azure/azure-monitor/app/availability?tabs=standard#availability-alerts), [dashboard](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard), and [queries](/azure/azure-monitor/logs/queries). This approach ensures that all critical components are quickly restored, minimizing downtime and maintaining service reliability.<br><br>

### Recommendations for Reliability

| Recommendation | Benefits |
|----------------|----------|
| ...            | ...      |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload. It is one of the most important aspects of any architecture. Azure Monitor provides features to employ both the principle of least privilege and defense in depth.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Application Insights. Use the following information to maximize the security of Application Insights and ensure that only authorized users access collected data.

### Design checklist for Security

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> * *SE:01 -* **Review the Azure Monitor [security baseline](/security/benchmark/azure/baselines/azure-monitor-security-baseline).** It provides guidance on security best practices to secure your cloud solutions on Azure, including Application Insights.<br><br>
>
> * *SE:02 & SE:08 -* **Ensure your application monitoring solution is secure by keeping Application Insights instrumentation up-to-date.** Follor our [SDK update guidance](/azure/azure-monitor/app/sdk-support-guidance#sdk-update-guidance) and update Application Insights SDKs (Classic API) annually. Although there's no specific guidance for the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable) yet, it's recommended to follow similar practices.<br><br>If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). This approach eliminates the need for manual SDK updates, requires no code changes, and eliminates the overhead of maintaining instrumentation code. This can enhance security by ensuring consistent application monitoring without manual intervention.<br><br>
>
> * *SE:03 -* **Define a strategy for [handling personal data in Application Insights](/azure/azure-monitor/logs/personal-data-mgmt).** Preferably, stop collecting personal data or obfuscate, anonymize, or adjust collected data to exclude it from being considered *personal*. To ensure ongoing compliance, regularly verify that the collection and handling of data, including IP addresses and personal data, [comply with relevant regulations](/azure/compliance/) such as GDPR.<br><br>For example, Application Insights by default [doesn't store IP addresses](/azure/azure-monitor/app/ip-collection) and it's recommended to not change that. However, if collecting and storing IP addresses is business-critical, verify the collection doesn't break any compliance requirements or local regulations.<br><br>
>
> * *SE:04 -* **Create intentional segmentation in your application monitoring design.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need. Use one resource per workload per environment (such as one for staging and one for production) to prevent mixing telemetry from different application versions. This will ensure data isolation and security, and help in applying environment-specific configurations and access controls.<br><br>
>
> * *SE:05 -* **Enable identity and acecss management (IAM).** Use [managed identities](/entra/identity/managed-identities-azure-resources/overview) and [Microsoft Entra ID](/azure/azure-monitor/app/azure-ad-authentication?tabs=net) to ensure only authorized users can access your resources. This also eliminates the need for credentials management, as [Azure manages, rotates, and protects these credentials](/entra/architecture/service-accounts-managed-identities).<br><br>
>
> * *SE:06 -* **Control network traffic.** Use [Azure Private Link](/azure/azure-monitor/logs/private-link-security) to access Azure services over a private endpoint, effectively isolating your traffic from the public internet. By doing so, you can apply defense-in-depth principles by creating localized network controls at all available network boundaries.<br><br>
>
> * *SE:07 -* **Use [Azure Monitor customer-managed key](/azure/azure-monitor/logs/customer-managed-keys?tabs=portal).** By default, data in Azure Monitor is encrypted with Microsoft-managed keys. You can use your own encryption key to protect the data and saved queries in your workspaces. Customer-managed keys in Azure Monitor give you greater flexibility to manage access controls to stored data.<br><br>
>
> * *SE:09 -* **Enhance data protection by securing storage systems and limiting access.** Visit the [Log Analytics service guide](azure-log-analytics.md#design-checklist-for-security) to learn about securing the data you're collecting.<br><br>

### Recommendations for Security

| Recommendation | Benefit |
|----------------|---------|
| ...            | ...     |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Insights and its environment.

For more information on how data charges are calculated for the underlying Log Analytics workspaces of your Application Insights resources, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/cost-usage).

### Design checklist for Cost Optimization

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md)
for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> * *CO:02 -* **Review [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/) to create a cost model.** Estimate the initial cost, run rates, and ongoing costs by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). Notice that Application Insights is billed through the Log Analytics workspace into which its log data ingested. To reduce costs, use [sampling](/azure/azure-monitor/app/sampling) on an Application Insights-level to reduce data traffic, data, and storage costs, while preserving a statistically correct analysis of application data.<br><br>
>
> * *CO:04 -* **[Set a daily cap](/azure/azure-monitor/logs/daily-cap#set-the-daily-cap) to limit unplanned charges for your workspace.** This shouldn't be used as a method to reduce costs, but rather as a preventative measure to ensure that you don't exceed a particular budget. Try to avoid reaching the daily cap since it can cause data to not be written in your Log Analytics workspace. If you have a workspace-based Application Insights (recommended), use the daily cap in workspace to limit ingestion and costs instead of using the cap in Application Insights.<br><br>
>
> * *CO:05 & CO:06 -* **Regularly review costs like regional pricing and available pricing tiers.** The most significant charges for most Azure Monitor implementations are typically ingestion and retention of data in your Log Analytics workspaces. For more information, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/logs/cost-logs) or visit the [Cost Optimization section](azure-log-analytics.md#design-checklist-for-cost-optimization) in the Log Analytics service guide.<br><br>
>
> * *CO:07 -* **Regularly remove or optimize legacy, unneeded, and underutilized components of your application monitoring solution.** [Edit ApplicationInsights.config](/azure/azure-monitor/app/configuration-with-applicationinsights-config) to turn off collection modules that you don't need. For example, you might decide that performance counters or dependency data aren't required.<br><br>For standard tests, the need for testing from different locations might vary between production and pre-production environments. Reduce the amount of locations accordingly. Use telemetry filters or processors in code to help optimize component costs by not logging or sampling irrelevant calls.<br><br>
>
> * *CO:08 & CO:14 -* **Optimize environment costs.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need and where to deploy them. Use one resource per workload per environment (such as one for staging and one for production) to prevent mixing telemetry from different application versions. Optimize log levels per environment to manage costs effectively. For example, production environments might require different logging levels compared to development environments.<br><br>Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace to minimize latency and reduce costs associated with cross-region telemetry.<br><br>
>
> * *CO:09 -* **Optimize flow costs.** Use telemetry filters or processors in code to help optimize component costs by not logging or sampling irrelevant calls. Decrease the sample rate for less critical flows and increase it for flows with high criticality. Use telemetry filters for non-essential telemetry.<br><br>
>
> * *CO:11 -* **Optimize code costs.** [Limit the number of Ajax calls](/azure/azure-monitor/app/javascript-sdk-configuration) that can be reported in every page view or disable Ajax reporting. If you disable Ajax calls, you also disable [JavaScript correlation](/azure/azure-monitor/app/distributed-trace-data).<br><br>Make sure to use updated Application Insights SDKs. Earlier versions of the ASP.NET Core SDK and Worker Service SDK [collect many counters by default](/azure/azure-monitor/app/eventcounters#default-counters-collected), which were collected as custom metrics. Use later versions to specify [only required counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).<br><br>
>
> * *CO:12 -* **Optimize scaling costs.** Use [preaggregated metrics](/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#preaggregated-metrics) to handle high-volume telemetry data more efficiently and limit the use of custom metrics. Notice that the Application Insights option to [enable alerting on custom metric dimensions](/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) can also increase costs.<br><br>If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). This approach eliminates the need for manual SDK updates, requires no code changes, and eliminates the overhead of maintaining instrumentation code. This can enhance security by ensuring consistent application monitoring without manual intervention.<br><br>
>
> * *CO:13 -* **Optimize personnel time.** Utilize Application Insights experiences such as the [application map](/azure/azure-monitor/app/app-map) and [failure and performance views](/azure/azure-monitor/app/failures-and-performance-views), and customize saved [queries](/azure/azure-monitor/logs/queries), [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview) for your specific business-needs. Keep track of your deployments and other events with [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations).<br><br>

### Recommendations for Cost Optimization

| Recommendation | Benefit |
|:---------------|:--------|
| ...            | ...     |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist for Operational Excellence

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Application Insights.

> [!div class="checklist"]
>
> * *OE:01* - **Integrate your application monitoring team members' specializations into a robust set of practices to instrument and monitor your workload.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need and where to deploy them. Use one resource per workload per environment (such as one for staging and one for production) to prevent mixing telemetry from different application versions.<br><br>Chose an instrumentation method (i.e., autoinstrumentation or manual instrumentation) that is best for your situation based on your business needs and [Supported environments, languages, and resource providers](/azure/azure-monitor/app/codeless-overview#supported-environments-languages-and-resource-providers).<br><br>
>
> * *OE:03 -* **Formalize ideation and planning processes.** Use [work item ingetration](/azure/azure-monitor/app/release-and-work-item-insights?tabs=work-item-integration) to easily create work items in GitHub or Azure DevOps that have relevant Application Insights data embedded in them.<br><br>
>
> * *OE:07 -* **Configure Application Insights to monitor the availability and responsiveness of your web application.** Use built-in features like [queries](/azure/azure-monitor/logs/queries) and [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard) based on your specific business needs. After you've deployed your application, set up recurring tests to monitor availability and responsiveness.<br><br>
>
> * *OE:08 -* **Develop an effective emergency operations practice.** Use [alerts](/azure/azure-monitor/alerts/alerts-overview) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview) to identify and respond to incidents. Clearly define human responsiblities. For example, if your workload fails, determine who reboots the application.<br><br>
>
> * *OE:11 -* **Clearly define your workload's safe deployment practices.** Use [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations) as part of your failure mitigation strategies to keep track of your deployments and other events.<br><br>

### Recommendations for Operational Excellence

| Recommendation | Benefit |
|----------------|---------|
| ...            | ...     |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist for Performance Efficiency

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Application Insights.

> [!div class="checklist"]
>
> * *PE:01 -* **Define performance targets, which are numerical values related to your application monitoring requirements.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need. Use one resource per workload per environment (such as one for staging and one for production) to prevent mixing telemetry from different application versions.
>
> * *PE:02 -* **Conduct capacity planning.**  Understand usage patterns and how much data is coming in, and review ingestion and sample rates.
>
> * *PE:03 -* **Select the right region for your application monitoring solution.** Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace to prevent latency and reliability issues, see [Create a resource](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#create-a-workspace-based-resource).
>
> * *PE:04 -* Evaluate [how many Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need. Monitoring mulitple applications or application components with a single Application Insights resource provides a hollistic view, but can also impact the performance of experiences like [Application Map](/azure/azure-monitor/app/app-map?tabs=net) and [Usage](/azure/azure-monitor/app/usage?tabs=aspnetcore).
>
> * *PE:07 -* **Optimize code and infrastructure.** If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). This approach eliminates the need for manual SDK updates, requires no code changes, and eliminates the overhead of maintaining instrumentation code.
>
> * *PE:08 -* **Understand usage patterns and how much data is coming in by reviewing ingestion and sample rates.** To optimize data usage, adjust them accordingly and reduce the amount of [custom metrics](/azure/azure-monitor/app/api-custom-events-metrics), for example ITelemetryProcessor.
>
> * *PE:09 -* **Prioritize critical flows.** Use Application Insights' [userflows](/azure/azure-monitor/app/usage?tabs=aspnetcore#user-flows---analyze-user-navigation-patterns) experience to understand usage patterns, which will allow you to prioritize critical flows.
>
> * *PE:10 -* **Optimize operational tasks.** Use [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations) for deployments and [custom events](/azure/azure-monitor/app/api-custom-events-metrics) for other actions.
>
> * *PE:11 -* **Use [Live Metrics stream](/azure/azure-monitor/app/live-stream?tabs=otel) and [availability alerts](/azure/azure-monitor/app/availability?tabs=standard#availability-alerts) to respond to live performance issues.** Plan how to address problems by incorporating clear lines of communication and responsibilities.
>
> * *PE:12 -* **Continuously optimize performance.** Use built-in features like [Smart Detection](/azure/azure-monitor/alerts/proactive-diagnostics), [queries](/azure/azure-monitor/logs/queries), and [dashboards](/azure/azure-monitor/app/overview-dashboard) to find components that show deteriorating performence.

### Recommendations for Performance Efficiency

| Recommendation | Benefit |
|:---------------|:--------|
| ...            | ...     |

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

