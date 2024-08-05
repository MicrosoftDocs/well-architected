---
title: Azure Well-Architected Framework perspective on Virtual Network
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure virtual network and its supported features.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 02/20/2024
ms.topic: conceptual
products:
  - azure-virtual-network
azure.category:
  - networking
---

# Azure Well-Architected Framework perspective on Virtual Network

Virtual Network is a fundamental building block for establishing a private network on Azure. It allows communication between with Azure resources, internet connectivity, and integration with on-premises systems. Additionally, it includes built-in filtering capabilities to make sure that only expected, allowed, and safe traffic reaches the components within the network boundaries.

This article assumes that as an architect you've reviewed [SE:06 Recommendations for networking and connectivity](/azure/architecture/guide/technology-choices/compute-decision-tree) and understand the key strategies behind the service-level guidance presented in this article. It also balances the approaches of all the [Azure Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are *recommendations* on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Virtual Network and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demonstrates the key recommendations: [Hub-spoke network topology in Azure](/azure/architecture/networking/architecture/hub-spoke).

##### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:

- Virtual networks, its subnets, and network interface cards (NICs)
- Private endpoints
- Network Security Groups (NSGs)
- IP addresses, allocations
- Route tables
- Network managers

There are other services associated with Virtual Network, such as the load balancers. Those services are covered in their respective guides. 

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features of Virtual Network and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>

##### Recommendations

| Recommendation|Benefit|
|-----------|-------- |
|Do, Don't, consider, this.. |Because it's your workload after all.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on App Service.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>

##### Recommendations

| Recommendation|Benefit|
|-----------|-------- |
|Do, Don't, consider, this.. |Because it's your workload after all.|


## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to your networking environment.

##### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>

##### Recommendations

| Recommendation|Benefit|
|-----------|-------- |
|Do, Don't, consider, this.. |Because it's your workload after all.|


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to your networking environment.

> [!div class="checklist"]
>

| Recommendation|Benefit|
|-----------|-------- |
|Do, Don't, consider, this.. |Because it's your workload after all.|


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on the key performance indicators.

> [!div class="checklist"]
>

| Recommendation|Benefit|
|-----------|-------- |
|Do, Don't, consider, this.. |Because it's your workload after all.|


## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Topic vs. topic**

- point 1
- point 2


:::image type="icon" source="../_images/trade-off.svg"::: **Topic vs. topic**

- point 1
- point 2


## Azure policies

Azure provides an extensive set of built-in policies related to Virtual Network and its dependencies. A set of Azure policies can audit some of the preceding recommendations. For example, you can check whether:

- 

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/app-service/policy-reference) and other policies that might affect the security of the network layer.




## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your web application instances.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)