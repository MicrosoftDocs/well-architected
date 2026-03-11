---
title: Architecture Best Practices for Azure Monitor Application Insights
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Application Insights.
author: kainawroth
ms.author: v-nawrothkai
ms.date: 11/26/2024
ms.topic: concept-article
ms.subservice: waf-service-guide
products:
  - azure-application-insights
  - azure-log-analytics
  - azure-monitor
azure.category:
  - devops
  - management-and-governance
---

# Architecture best practices for Application Insights

Application Insights is an extensible application performance management service that you can use to monitor live applications and automatically detect performance anomalies. It includes powerful analytics tools to help you diagnose problems and understand how users interact with your application. This architecture guide provides best practices for Application Insights based on the five pillars of the [Azure Well-Architected Framework pillars](/azure/architecture/framework/).

### Technology scope

This guide focuses on the interrelated decisions for the following Azure resources:

- Application Insights
- Log Analytics workspaces

> [!NOTE]
> Application Insights stores and exposes data in a Log Analytics workspace. When you evaluate your metrics, keep Log Analytics as a critical dependency on Application Insights. For more information, see [Well-Architected Framework perspective on Log Analytics](azure-log-analytics.md).

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements and keep in mind the dependency on Log Analytics. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Align your application monitoring design with business objectives and avoid unnecessary complexity.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) that you need and where to deploy them.
>
> - **Configure sampling based on the criticality of the data.** Identify the user and system flows of your workload, determine the events that you want to gather, and configure sampling accordingly.
>     - For manually instrumented applications, see [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-configuration?tabs=aspnetcore#enable-sampling) or [Application Insights SDKs (Classic API)](/azure/azure-monitor/app/sampling-classic-api).
>     - For autoinstrumentation, sampling is described in environment-specific articles. For more information, see [Configure sampling in Azure Functions](/azure/azure-functions/configure-monitoring).
>
> - **Create a comprehensive resiliency plan.**
>     1. Start by conducting a failure mode analysis to identify potential scenarios when Application Insights might fail or become unreachable. These reasons can include network problems, authentication problems, or service disruptions.
>     1. Determine how your workload should behave if Application Insights is unreachable at boot or during runtime based on the criticality of application monitoring to your business goals. Define and document the expected behavior of your workload.
>     1. Test your resiliency plan. For example, you can validate network failures by using a network security group rule, and authenticate failures by changing the connection string.
>
> - **Plan for workspace resiliency and recovery by defining targets for your data collection components.**
>     1. Assess the criticality of the data that you collect and determine if it needs to be recoverable.
>     1. Review service limits for [Application Insights](/azure/azure-monitor/service-limits#application-insights) and [Log Analytics workspace](/azure/azure-monitor/service-limits#log-analytics-workspaces) to understand restrictions on data collection and retention, and other aspects of the service.
>     1. Consider moving to a [dedicated cluster](/azure/azure-monitor/logs/logs-dedicated-clusters) to take advantage of workspace resilience where the availability of Application Insights telemetry within the retention period defined is business critical.
>     1. Use [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) to export platform logs and metrics to the destination of your choice (for example, a storage account) for backup and recovery purposes.
>
> - **Implement a timely and reliable scaling strategy to plan for data ingestion growth.** Monitor and adjust limits on sampling and data ingestion as traffic grows to prevent data loss because of sampling or exceeding the [daily cap](/azure/azure-monitor/logs/daily-cap). This helps ensure that your data ingestion process scales effectively with traffic increases.
>
> - **Ensure quick recovery of your application monitoring solution in case of service failure.** Adopt infrastructure as code and use [Bicep templates](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#azure-resource-manager-templates) to create or re-create your user experience in Application Insights, including [alerts](/azure/azure-monitor/app/availability?tabs=standard#availability-alerts), [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard), and [queries](/azure/azure-monitor/logs/queries). This approach helps ensure that all critical components are quickly restored, which minimizes downtime and maintains service reliability.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|----------|
| Use one Application Insights resource per workload per environment, such as one for development, one for staging, and one for production. | Using multiple Application Insights resources prevents mixing telemetry from different application versions and helps ensure that a misconfiguration of one resource doesn't affect the logging for another environment. |
| Deploy your Application Insights resource in the same region as the underlying [Log Analytics workspace](/azure/azure-monitor/logs/log-analytics-workspace-overview). | Because Application Insights requires Log Analytics to function properly, having both resources in different regions doubles the chance for a regional failure to cause problems. |
| Implement a resilient workspace design by using [best practices for Azure Monitor Logs](/azure/azure-monitor/best-practices-logs). | Best practices help ensure continuous and robust monitoring by minimizing disruptions. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Application Insights.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Review the [Azure Monitor security baseline](/security/benchmark/azure/baselines/azure-monitor-security-baseline).** It provides guidance on security best practices to help secure your cloud solutions on Azure, including Application Insights.
>
> - **Keep Application Insights instrumentation up to date to help ensure that your application monitoring solution is secure.** Follow our [SDK update guidance](/azure/azure-monitor/app/sdk-support-guidance#sdk-update-guidance) and update Application Insights SDKs (Classic API) at least once a year. We recommend that you follow similar practices for the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable).
>
> - **Define a strategy for [handling personal data in Application Insights](/azure/azure-monitor/logs/personal-data-mgmt).** To ensure ongoing compliance, regularly verify that the collection and handling of data, including IP addresses and personal data, [complies with relevant regulations](/azure/compliance/), such as GDPR.
>
> - **Create intentional segmentation in your application monitoring design.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) that you need.
>
> - **Use [Azure Monitor customer-managed key](/azure/azure-monitor/logs/customer-managed-keys?tabs=portal).** By default, data in Azure Monitor is encrypted with Microsoft-managed keys. You can use your own encryption key to protect the data and saved queries in your workspaces. Customer-managed keys in Azure Monitor give you greater flexibility to manage access controls to stored data.
>
> - **Control network traffic.** Consider private connectivity for accessing Azure services. Private connectivity effectively isolates your traffic from the public internet. Data flows for private networking include both data ingestion and query operations, each targeting distinct endpoints. These endpoints can be managed independently. This approach allows you to configure private ingestion while maintaining public query access, or vice versa. By doing so, you can apply defense-in-depth principles by creating localized network controls at all available network boundaries.
>
> - **Enhance data protection by securing storage systems and limiting access.** Visit the [Log Analytics service guide](/azure/well-architected/service-guides/azure-log-analytics#security) to learn about how to secure the data that you collect.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). | This approach eliminates the need for manual SDK updates, requires no code changes, and eliminates the overhead of maintaining instrumentation code. It can also enhance security by helping ensure consistent application monitoring without manual intervention. |
|Use [managed identities](/entra/identity/managed-identities-azure-resources/overview) and [Microsoft Entra ID](/azure/azure-monitor/app/azure-ad-authentication?tabs=net) for your authentication and authorization needs.|Eliminates the need for credentials management because [Azure manages, rotates, and protects these credentials](/entra/architecture/service-accounts-managed-identities).|
| Stop collecting personal data or obfuscate, anonymize, or adjust collected data. By default, Application Insights [doesn't store IP addresses](/azure/azure-monitor/app/ip-collection). We recommend that you keep this default setting. | Excludes your data from being considered *personal* and prevents breaking any compliance requirements or local regulations. |
| Use one Application Insights resource per workload per environment. For example, use one for development, one for staging, and one for production. | Using multiple Application Insights resources helps ensure data isolation and security, and makes it easier to apply environment-specific configurations and access controls. |
| Use [Azure Private Link](/azure/azure-monitor/logs/private-link-security) to access Azure services over a private endpoint. Follow the configuration and limitations. Avoid connecting your AMPLS to multiple virtual networks that share the same DNS because this causes *wrong private endpoint* errors. | A properly configured AMPLS topology enhances the confidentiality of your workload's logs by keeping logging traffic on fully private networks. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Insights and its environment.

For more information on how data charges are calculated for the underlying Log Analytics workspaces of your Application Insights resources, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/cost-usage).

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Review [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/) to create a cost model.** Estimate the initial cost, run rates, and ongoing costs by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). Notice that Application Insights is billed through the Log Analytics workspace into which its log data ingested.
>
> - **Tune the amount of data collected.**
>     - Use [sampling](/azure/azure-monitor/app/sampling) on an Application Insights-level to reduce data traffic and storage costs while preserving a statistically correct analysis of application data.
>     - Application Insights has several possible [log sources](/azure/azure-monitor/app/app-insights-overview#logging-frameworks). Use log levels to tune and reduce trace log telemetry.
>
> - **Limit unplanned charges for your workspace.** You can set a daily cap in both Application Insights and Log Analytics.
>
> - **Review costs like regional pricing and available pricing tiers regularly.** The most significant charges for most Azure Monitor implementations are typically ingestion and retention of data in your Log Analytics workspaces. For more information, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/logs/cost-logs) or the [Cost Optimization section](/azure/well-architected/service-guides/azure-log-analytics#cost-optimization) in the Log Analytics service guide.
>
> - **Remove or optimize legacy, unneeded, and underutilized components of your application monitoring solution regularly.** [Edit ApplicationInsights.config](/azure/azure-monitor/app/configuration-with-applicationinsights-config) to turn off collection modules that you don't need. For example, you might decide that performance counters or dependency data isn't required. Use telemetry filters or processors in code to help optimize component costs by not logging or sampling irrelevant calls.
>
> - **Optimize environment costs.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need and where to deploy them. Optimize log levels and metrics collected per environment to manage costs effectively. For example, production environments might require different logging levels compared to development environments.
>
> - **Optimize flow costs.** Use telemetry filters or processors in code to help optimize component costs by not logging or sampling irrelevant calls.
>
> - **Optimize code costs.** Make sure to use updated Application Insights SDKs. Earlier versions of the ASP.NET Core SDK and Worker Service SDK [collect many counters by default](/azure/azure-monitor/app/eventcounters#default-counters-collected), which were collected as custom metrics. Use later versions to specify [only required counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).
>
> - **Optimize personnel time.** Use Application Insights experiences such as the [application map](/azure/azure-monitor/app/app-map) and [failure and performance views](/azure/azure-monitor/app/failures-and-performance-views), and customize saved [queries](/azure/azure-monitor/logs/queries), [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview) for your specific workload needs. Keep track of your deployments and other events with [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations).

### Configuration recommendations

| Recommendation | Benefit |
|:---------------|:--------|
| [Set a daily cap in both Application Insights and Log Analytics](/azure/azure-monitor/logs/daily-cap#when-to-use-a-daily-cap), but try to avoid reaching it. | Reaching the daily cap can cause data to not be written in your Log Analytics workspace. Setting a cap is a risky but effective solution for throttling spend. |
| For standard availability tests, the need for testing from different locations might vary between production and preproduction environments. Reduce the number of locations accordingly. | Tests incur costs, so only have enough tests to give you confidence in your workload's heath. |
| Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace. | This approach minimizes latency and reduces costs associated with cross-region telemetry. |
| Decrease the sample rate for less critical flows and increase it for flows with high criticality. Use telemetry filters for nonessential telemetry. | Data volume is a cost driver when designing your Application Insights integration. Reducing data volume through sample rates and filters is a potential solution to keeping your data collection under control. |
| [Limit the number of Ajax calls](/azure/azure-monitor/app/javascript-sdk-configuration) that can be reported in every page view, or disable Ajax reporting. If you disable Ajax calls, you also disable [JavaScript correlation](/azure/azure-monitor/app/distributed-trace-data). | Data volume is a cost driver when designing your Application Insights integration for your client application. Reducing data volume through reduced client page reporting is a potential solution to keeping your data collection costs under control. |
| Use [preaggregated metrics](/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#preaggregated-metrics) to handle high-volume telemetry data more efficiently and limit the use of custom metrics. However, the Application Insights option to [enable alerting on custom metric dimensions](/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) can also increase costs. Use metrics to ensure that cost savings are being obtained. | All custom metrics are stored in both logs and metric stores. Preaggregated metrics reduce storage costs related to custom metrics. |
| If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). | This approach optimizes software engineering time by eliminating the need for manual SDK updates, code changes related to new versions, and the overhead of maintaining instrumentation code. |
| Limit unwanted trace logging:<br><br>• Remove health checks. See *Filter out noisy traces* in [Optimizing Cost using the Azure Monitor OpenTelemetry Distro](https://techcommunity.microsoft.com/blog/azureobservabilityblog/optimizing-cost-using-the-azure-monitor-opentelemetry-distro/4115870).<br>• For Azure Kubernetes Service (AKS), adjust [control plane and data plane logs](/azure/aks/monitor-aks#azure-monitor-resource-logs).<br>• For Azure Functions, [adapt log levels and scope](/azure/azure-functions/configure-monitoring) to optimize log volume. | Limiting unwanted trace logging reduces the amount of stored data, which can lower storage costs. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Application Insights.

> [!div class="checklist"]
>
> - **Integrate your application monitoring team members' specializations into a robust set of practices to instrument and monitor your workload.** Choose an instrumentation method (for example, autoinstrumentation or manual instrumentation) that's best for your situation based on your business needs and [supported environments, languages, and resource providers](/azure/azure-monitor/app/codeless-overview#supported-environments-languages-and-resource-providers).
>
> - **Ensure optimal performance of your application monitoring solution by keeping Application Insights instrumentation up to date.** Follow our [SDK update guidance](/azure/azure-monitor/app/sdk-support-guidance#sdk-update-guidance) and update Application Insights SDKs (Classic API) at least once a year. It's recommended to follow similar practices for the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable). Using the latest SDK or Distro versions [ensures access to support services](/azure/azure-monitor/app/sdk-support-guidance) and provides the latest functionality and bug fixes.
>
> - **Formalize ideation and planning processes.** Use [work item ingetration](/azure/azure-monitor/app/release-and-work-item-insights?tabs=work-item-integration) to easily create work items in GitHub or Azure DevOps that have relevant Application Insights data embedded in them.
>
> - **Configure Application Insights to monitor the availability and responsiveness of your web application.** Use built-in features like [queries](/azure/azure-monitor/logs/queries) and [dashboards](/azure/azure-monitor/app/overview-dashboard#create-a-new-dashboard) based on your specific business needs. After you deploy your application, set up recurring tests to monitor availability and responsiveness.
>
> - **Develop an effective emergency operations practice.** Use [alerts](/azure/azure-monitor/alerts/alerts-overview) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview) to identify and respond to incidents. Clearly define human responsibilities. For example, determine who reboots the application if your workload fails.
>
> - **Clearly define your workload's safe deployment practices.** Use [Release Annotations](/azure/azure-monitor/app/release-and-work-item-insights?tabs=release-annotations) as part of your failure mitigation strategies to keep track of your deployments and other events.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| If your business needs and hosting environment don't require manual instrumentation, consider using [autoinstrumentation](/azure/azure-monitor/app/codeless-overview). | This approach optimizes software engineering time by eliminating the need for manual SDK updates, code changes related to new versions, and the overhead of maintaining instrumentation code. |
| If your business needs require manual instrumentation, adopt the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable). | Avoid a future forced migration from Application Insights SDKs (Classic API) by adopting an OpenTelemetry-based instrumentation method. |
| Use [connection strings](/azure/azure-monitor/app/migrate-from-instrumentation-keys-to-connection-strings). | Make telemetry ingestion more reliable and remove dependencies on global ingestion endpoints. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Application Insights.

> [!div class="checklist"]
>
> - **Define performance targets related to your application monitoring requirements.** Determine the [number of Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need.
>
> - **Conduct capacity planning.** Understand usage patterns and how much data is coming in, and review ingestion and sample rates.
>
> - **Select the right region for your application monitoring solution.** Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace to prevent latency and reliability problems. See [Create a resource](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#create-a-workspace-based-resource).
>
> - **Evaluate [how many Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need.** Monitoring mulitple applications or application components with a single Application Insights resource provides a holistic view, but can also affect the performance of experiences like [Application Map](/azure/azure-monitor/app/app-map?tabs=net) and [Usage](/azure/azure-monitor/app/usage?tabs=aspnetcore).
>
> - **Optimize code and infrastructure.** Regularly evaluate custom Application Insights code to reduce complexity, improve performance, and ensure that the code is up to date.
>
> - **Understand usage patterns and how much data is coming in by reviewing ingestion and sample rates.** To optimize data usage, adjust them accordingly and reduce the amount of [custom metrics](/azure/azure-monitor/app/api-custom-events-metrics), for example ITelemetryProcessor.
>
> - **Continuously optimize performance.** Use built-in features like [Smart Detection](/azure/azure-monitor/alerts/proactive-diagnostics), [queries](/azure/azure-monitor/logs/queries), and [dashboards](/azure/azure-monitor/app/overview-dashboard) to find components that show deteriorating performence.

### Configuration recommendations

| Recommendation | Benefit |
|:---------------|:--------|
| Use one Application Insights resource per workload per environment (such as one for development, one for staging, and one for production). | This approach prevents mixing telemetry from different application versions. |
| When applicable, ensure that [profiling frequency and duration is set appropriately](/azure/azure-monitor/profiler/profiler-settings). | Avoid adding excessive overhead to the running process. |

## Azure policies

Azure provides an extensive set of built-in policies related to Application Insights and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Application Insights components should prevent log ingestion public networks, or sources that Microsoft Entra ID doesn't authenticate.
- You should enforce linking of the Application Insights component to a Log Analytics workspace to encrypt logs.

For comprehensive governance, review the [Azure Policy built-in definitions for Application Insights](/azure/governance/policy/samples/built-in-policies#monitoring) and other policies that might affect the security of the application performance monitoring solution.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Related content

- [Application Insights](/azure/azure-monitor/app/app-insights-overview)
- [Monitoring and diagnostics guidance](/azure/architecture/best-practices/monitoring)
- [Azure Well-Architected Framework perspective on Log Analytics](./azure-log-analytics.md)
- [Enable application monitoring in Azure App Service for .NET, Node.js, Python, and Java applications](/azure/azure-monitor/app/codeless-app-service)
- [Enable Azure Monitor OpenTelemetry for .NET, Node.js, Python, and Java applications](/azure/azure-monitor/app/opentelemetry-enable)