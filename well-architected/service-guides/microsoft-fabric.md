---
title: "Architecture Best Practices for Microsoft Fabric"
description: "Learn best practices for architecting Microsoft Fabric solutions with recommendations for reliability, security, cost optimization, operational excellence, and performance efficiency."
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: conceptual
ms.date: 11/17/2025
products:
 - microsoft-fabric
azure.category:
 - analytics
---

# Architecture best practices for Microsoft Fabric

Microsoft Fabric is a comprehensive, unified analytics platform that brings together data movement, processing, ingestion, transformation, real-time event routing, and report building in a single environment. It's designed to help organizations build and deploy complete analytics solutions at scale, providing an integrated platform for data teams to collaborate efficiently across the entire data lifecycle. Across these diverse use cases for Microsoft Fabric, there are common considerations and best practices for using the platform effectively. This article addresses these considerations and gives architectural recommendations that are mapped to the principles of the Well-Architected Framework pillars.

It's assumed that as an architect, you've reviewed the analytics platform options available and chosen Microsoft Fabric as the comprehensive data platform for your workload.

**Technology scope**

This review focuses on the interrelated decisions for Microsoft Fabric components and services:

- TBD

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
>

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Configure [Microsoft Fabric capacity](/fabric/admin/service-admin-premium-capacity-management) with appropriate scaling policies and reserve additional capacity units to handle peak workloads. Monitor capacity utilization metrics and implement automated scaling triggers based on performance thresholds. | Prevents capacity exhaustion during peak periods while maintaining cost efficiency through dynamic capacity allocation that scales based on actual workload demands and performance requirements. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Microsoft Fabric.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Configure [Microsoft Entra ID integration](/fabric/admin/service-admin-portal-tenant-settings) with conditional access policies and multi-factor authentication for all Microsoft Fabric workspace access. Enable automatic user provisioning and implement role-based access controls aligned with organizational security policies. | Provides enterprise-grade authentication and authorization controls that eliminate password-related security risks while ensuring compliance with organizational security standards and regulatory requirements. |

## Cost Optimization

The purpose of the Cost Optimization pillar is to **manage costs to maximize the value delivered**.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs within the Microsoft Fabric architecture.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md). Define policies and procedures to continuously monitor and optimize costs while meeting your performance requirements.

> [!div class="checklist"]
>

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Monitor [Microsoft Fabric capacity metrics](/fabric/admin/service-admin-premium-capacity-management) and implement automated capacity scaling policies based on usage patterns. Configure alerts for capacity utilization thresholds to prevent performance degradation and cost overruns. | Optimizes capacity costs by automatically adjusting resources based on actual demand while preventing performance issues during peak usage periods, resulting in 20-40% cost savings compared to fixed capacity allocation. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Microsoft Fabric.

> [!div class="checklist"]
>
> 

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Configure [Microsoft Fabric monitoring](/fabric/admin/service-admin-portal-tenant-settings) with comprehensive telemetry collection across all workspace components. Implement automated alerting for capacity utilization, pipeline failures, and data quality issues with appropriate escalation procedures. | Provides unified operational visibility across the entire Microsoft Fabric platform, enabling proactive issue detection and rapid response to maintain service availability and performance standards. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Microsoft Fabric. 

> [!div class="checklist"]
>
> - **Do capacity planning.**  Evaluate workload characteristics to determine the optimal capacity units (CUs) and capacity unit seconds. CUs represent provisioned compute power, while CU-seconds track actual consumption. The goal is choose the right SKU to get predictable performance without over- or under-provisioning. Unused CUs don't carry over so careful estimation is required to avoid wasted or insufficient resources.
>   
>   Avoid a one-size-fits-all approach, as different workloads consume capacity in different ways. Classify workloads by behavior and features: for example, data warehouse resources are used only when queries execute; interactive workloads require planning for peak demand; continuously running background tasks can be sized based on daily averages. Understand those patterns to map CU consumption and determine whether to increase capacity, redesign workload distribution, or leverage autoscalingy averages. Map these patterns to CU usage to decide whether to increase capacity, redesign workload distribution, or use autoscaling.
>
> - **Validate performance baseline with iterative testing**. Don't rely only on estimators or theoretical calculations. Deploy a representative sample of your workload to measure actual capacity utilization. Also pay attention to resource utilization (CPU, memory), concurrency levels, and response times and throughput for critical operations. 
> 
>   Use these measurements to extrapolate capacity requirements for higher data volumes, increased process frequency, or greater concurrency.
>
>   Because workloads may scale non-linearly, this approach should be iterative. Adjust capacity based on observed performance, retest, and refine your estimates. Real-world testing and measurement are a must to validate assumptions so that capacity is right sized and performance bottlenecks are avoided.
>
> - **Be aware of the quotas and limits**. Each Fabric SKU also enforces fixed compute, memory, and service-level quotas that can become hard ceilings if they are not explicitly accounted for in the architecture. For example, for Spark there are limits on vCore. Concurrency can be a hidden constraint. A large Spark job, a heavy SQL query, or a pipeline with parallel activities can consume a significant share of available CUs. Adding capacity doesn't necessarily improve performance. Design for isolation so that one workload doesn't monopolize resources and degrade others. Other strategies include capping concurrent executions for non-critical workloads. 
>
>   Each Azure subscription has region-specific quotas on the number of CUs that can be provisioned, which can limit workload deployment in certain regions. If quotas are reached, you can request an increase or consider deploying workloads in adjacent regions, balancing proximity to users and data with available capacity. Be aware that even with the same SKU, performance may vary across regions.
>
>   Workspaces also have limits on the number of items and users that can be assigned.
>   
>   Lower SKUs, like F8, can lead to memory and compute constraints and aren't recommended for production workloads.
>   
> - **Account for dependencies**. 

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
|Take advantage of the [**Microsoft Fabric Capacity Estimator**](https://www.microsoft.com/microsoft-fabric/capacity-estimator?msockid=0d8bfe5e72c36a7b1318edb276c364ab) to get an initial estimate the capacity units (CUs) required for your workload. |Helps ensure workloads have the right resources to run without performance degradation and also and supports in better scaling decisions.|
|What tools are recommended for performance load testing?||
|Choose the optimal [**Microsoft Fabric SKU**](/fabric/enterprise/licenses#capacity) based on your capacity planning.|Ensures that workloads have the right level of compute and memory resources, supporting reliable performance, efficient scaling, and alignment with expected usage patterns.|
|**To be continued**:||

## Azure policies

Azure provides an extensive set of built-in policies related to Microsoft Fabric and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Microsoft Fabric workspaces implement appropriate capacity governance policies
- Microsoft Fabric workspaces use encryption for data protection requirements
- Microsoft Fabric workspaces have proper access controls and authentication configured
- Microsoft Fabric workspaces implement required monitoring and logging capabilities
- Microsoft Fabric workspaces follow organizational naming and tagging conventions
- Microsoft Fabric workspaces are deployed in approved geographic regions for compliance

For more information about governance, review the [Azure Policy built-in definitions](/azure/governance/policy/) and other policies that might affect the security of the analytics platform.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists.

:::image type="icon" source="../_images/trade-off.svg"::: **Analyze performance and cost trade-offs**

Balancing performance and cost in Microsoft Fabric requires careful consideration of capacity allocation, workload distribution, and usage patterns. Over-provisioning leads to unnecessary costs, while under-provisioning can impact user experience and analytical performance.

## Scenario architecture

Foundational architecture that demonstrates the key recommendations: [Analytics end-to-end with Microsoft Fabric](/azure/architecture/example-scenario/analytics/analytics-end-to-end-fabric).

## Related content

- [Microsoft Fabric documentation](/fabric/)
- [Microsoft Fabric best practices](/fabric/get-started/fabric-adoption-roadmap)
