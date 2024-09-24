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

Modern implementations of Application Insights store data in a [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview#overview-of-log-analytics-in-azure-monitor) workspace. For more information, see [Azure Well-Architected Framework perspective on Log Analytics](azure-log-analytics.md).

> [!div class="checklist"]
>
> * *RE:01 -* Evaluate [how many Application Insights resources](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy) you need.
> * *RE:01 -* Deploy your Application Insights resource in the same region as the underlying Log Analytics workspace to prevent latency and reliability issues, see [Create a resource](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#create-a-workspace-based-resource).
> * *RE:01 -* Determine which instrumentation method (i.e., autoinstrumentation or manual instrumentation) is best for your situation based on [Supported environments, languages, and resource providers](/azure/azure-monitor/app/codeless-overview#supported-environments-languages-and-resource-providers).
> * *RE:02 -* Configure sampling based on the criticality of the data and the user and system flows of the workload. For more information, see the following instrumentation-specific documentation:<br>&emsp;• Autoinstrumentation (*Article missing.*)<br>&emsp;• [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-configuration?tabs=aspnetcore#enable-sampling)<br>&emsp;• [Application Insights SDKs (Classic API)](/azure/azure-monitor/app/sampling-classic-api)
> * *RE:03 -* Use Failure Mode Analysis (FMA) to identify different scenarios where Application Insights could fail or be unreachable, such as network, authentication, or service issues. Determine how your workload should behave if Application Inights is unreachable at boot or during runtime based on how critical application monitoring is to your business goals. Define the expected behavior of your workload in those cases and test your resiliency plan.
> * *RE:04 -* Plan for workspace resilience and recovery. Determine how critical the data is you're collecting and if it must be recoverable. Review service limits for [Application Insights](/azure/azure-monitor/service-limits#application-insights) and the underlying [Log Analytics workspace](/azure/azure-monitor/service-limits#log-analytics-workspaces) to understand restrictions on data collection and retention, and other aspects of the service.
> * *RE:04 -* Look up Microsoft SLA for Application Insights to understand what is not covered and how it could impact your workload reliability.
> * *RE:06 -* Plan for data ingetion growth appropriately. Monitor and adjust limits on sampling and data ingestion as traffic grows to avoid losing any data that would otherwise get lost in sampling or exceed the daily cap.
> * *RE:08 -* Validate network failures with NSG (Network Security Group) rules.
> * *RE:09 -* Use infrastructure as a service and leverage [ARM and Bicep templates](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#azure-resource-manager-templates) to create alerts, dashboard, and queries. This will ensure a quick recovery in case of service failure.
> * *Other -* Keep Log Analytics as a critical dependency on Application Insights when evaluating your metrics.

**Notes**

* *RE:05 and RE:10 don't apply to Reliability in Application Insights.*
* *RE:07 is only part of Recommendations.*

### Recommendations for Reliability

| Recommendation | Benefits |
|----------------|----------|
| *RE:01 -* **Use different Application Insights resources for different development stages of your application.** For example, one Application Insights instance for staging and one for production. | Using multiple Application Insights resources will prevent mixing telemetry from the new version and the already released version of your application. |
| *RE:01 -* **Use a single Application Insights resource for traces across services.** For example, if you want to trace a request from service A to service B, both services should send telemetry to the same Application Insights resource. | This will allow you to get a hollistic picture and visualize requests across services on the [Application Map](/azure/azure-monitor/app/app-map?tabs=net) in Application Insights. |
| *RE:01 -* Use [autoinstrumentation](/azure/azure-monitor/app/codeless-overview), if available. | Autoinstrumentation doesn't require any code changes and eliminates the overhead of maintaining instrumentation code. |
| *RE:01 -* Upgrade from classic to workspace-based Application Insights resources. | Data is ingested more rapidly via [Log Analytics streaming ingestion](/azure/azure-monitor/app/convert-classic-resource), which could be critical in time-sensitive scenarios. |
| *RE:02 -* **Identify the sample rate and events you want to gather from various flows.** For highly critial data, adjust the sample rate to capture more data. | Sampling is the recommended way to reduce telemetry traffic and throttling while preserving a statistically correct analysis of application data. |
| *RE:04 -* Use [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) to export platform logs and metrics to the destination of your choice (e.g., a storage account) for backup and recovery purposes. | ... |
| *RE:07 -* For [standard availability tests](/azure/azure-monitor/app/availability?tabs=standard) use multiple regions and enable the *retry* feature. | ... |
| *Other -* Implement a resilient workspace design by using [best practices for Azure Monitor Logs](/azure/azure-monitor/best-practices-logs). | This will ensure continuous and robust monitoring by minimizing disruptions. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload. It is one of the most important aspects of any architecture. Azure Monitor provides features to employ both the principle of least privilege and defense in depth.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Application Insights. Use the following information to maximize the security of Application Insights and ensure that only authorized users access collected data.

### Design checklist for Security

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> * *SE:01 -* Review the Azure Monitor [security baseline](/security/benchmark/azure/baselines/azure-monitor-security-baseline). This topic provides guidance on security best practices.
> * *SE:02 -* Keep Application Insights instrumentation up-to-date. Refer to our [SDK update guidance](/azure/azure-monitor/app/sdk-support-guidance#sdk-update-guidance) for the Application Insights SDK. While there's no such guidance for the [Azure Monitor OpenTelemetry Distro](/azure/azure-monitor/app/opentelemetry-enable), it's generally recommended to follow similar principles. Autoinstrumented applications don't require manual updates.
> * *SE:03 -* Define a strategy for [handling personal data in Azure Monitor Logs and Application Insights](/azure/azure-monitor/logs/personal-data-mgmt). Preferably, stop collecting personal data or obfuscate, anonymize, or adjust collected data to exclude it from being considered *personal*. This reduces the risk of handling personal data and ensures compliance with data protection regulations.
> * *SE:04 -* Determine the [appropriate number of Application Insights resources to deploy](/azure/azure-monitor/app/create-workspace-resource?tabs=bicep#how-many-application-insights-resources-should-i-deploy).
> * *SE:05 -* Use [Microsoft Entra ID](/azure/azure-monitor/app/azure-ad-authentication?tabs=net) to enable identity and acecss management (IAM), and to ensure only authenticated telemetry is ingested in your Application Insights resources. Note that using managed identities is a key security practice.
> * *SE:06 -* Use [Azure Private Link](/azure/azure-monitor/logs/private-link-security) to ensure your monitoring data is only accessed through authorized private networks.
> * *SE:07 -* Use [Azure Monitor customer-managed key](/azure/azure-monitor/logs/customer-managed-keys?tabs=portal). Data in Azure Monitor is encrypted with Microsoft-managed keys. You can use your own encryption key to protect the data and saved queries in your workspaces. Customer-managed keys in Azure Monitor give you greater flexibility to manage access controls to logs.
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
> * ...

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
> * ...

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
> * Review ingestion and sample rates.
> * Select the right region for your Application Insights deployment.
> * Use a single Application Insights resource to get a hollistic view.
> * Use autoinstrumentation when available.
> * Reduce custom metrics like TelemetryProcessor.
> * Use userflows.
> * Release Annotation for deployments, custom events for other actions (Indexer).
> * Use Live Metrics stream and alerts.
> * Smart Detection, queries, dashboards.

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
