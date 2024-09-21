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

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the `offering-specific-aspects`. Extend the strategy to include more approaches
as needed.

Modern implementations of Application Insights store data in a [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview#overview-of-log-analytics-in-azure-monitor) workspace. For more information, see [Azure Well-Architected Framework perspective on Log Analytics](azure-log-analytics.md).

> [!div class="checklist"]
>
> ...

### Recommendations

| Recommendation | Benefits |
|----------------|----------|
| ...            | ...      |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload. It is one of the most important aspects of any architecture. Azure Monitor provides features to employ both the principle of least privilege and defense in depth.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Application Insights. Use the following information to maximize the security of Application Insights and ensure that only authorized users access collected data.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - ...

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| ...            | ...     |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Insights and its environment.

For more information on how data charges are calculated for the underlying Log Analytics workspaces of your Application Insights resources, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/cost-usage).

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md)
for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - ...

### Recommendations

| Recommendation | Benefit |
|:---------------|:--------|
| ...            | ...     |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Application Insights.

> [!div class="checklist"]
>
> - ...

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| ...            | ...     |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Application Insights.

> [!div class="checklist"]
>
> ...

### Recommendations

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

Currently, Azure offers no Azure Advisor recommendations related Application Insights.

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
