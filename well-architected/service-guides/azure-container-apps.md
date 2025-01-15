---
title: Azure Well-Architected Framework review for Azure Container Apps (ACA)
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Container Apps (ACA).
author: igorjnzl
ms.author: igorjovovic
ms.topic: conceptual
ms.date: 01/16/2024
ms.product: azure-container-apps
azure.category:
  - containers
---

# Well-Architected Framework perspective on Azure Container Apps


[Introductory paragraph]


This article assumes that as an architect, you've reviewed the [update with decision tree here]and chose Container Apps as the compute platform for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-decision-tree
https://learn.microsoft.com/en-us/azure/architecture/guide/choose-azure-container-service



>**How to use this guide**
>
>Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
>Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Container Apps and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments. 
>
>Foundational architecture that demonstrates the key recommendations:
>[Baseline highly available, zone-redundant web application architecture](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- Azure Container Apps





## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures.**

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

[Add your content.]

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...



#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |


### Azure Policy



## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles]
(/azure/well-architected/security/security-principles)
provide a high-level design strategy for achieving those goals by
applying approaches to the technical design of [Azure offering].



#### Design checklist

[Add your content.]

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...



#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |


For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles).



### Azure Policy definitions


## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Gateway and its environment.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Kubernetes Service** from the available products. You can test different configuration and payment plans in the calculator.



#### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |



### Policy definitions

While there are no built-in policies that are related to cost optimization, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional cost optimization constraints you'd like to enforce in your cluster and workload architecture.

### Cloud efficiency

Making workloads more [sustainable and cloud efficient](../sustainability/sustainability-get-started.md), requires combining efforts around **cost optimization**, **reducing carbon emissions**, and **optimizing energy consumption**. Optimizing the application's cost is the initial step in making workloads more sustainable.

Learn how to build sustainable and efficient AKS workloads, in [Sustainable software engineering principles in Azure Kubernetes Service (AKS)](/azure/aks/concepts-sustainable-software-engineering).

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management.**

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

#### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]


#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |



For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

Azure Advisor also makes recommendations on a subset of the items listed in the policy section below, such unsupported AKS versions and unconfigured diagnostic settings. Likewise, it makes workload recommendations around the use of the default namespace.

### Policy definitions

Azure Policy offers various built-in policy definitions that apply to both the Azure resource and AKS like standard policy definitions, and using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.



#### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]



#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |



## Azure policies

Azure provides an extensive set of built-in policies related to App Service and its dependencies. A set of Azure policies can audit some of the preceding recommendations. For example, you can check whether:

[Add your content.]

- [policy-description]
- [policy-description]
  ...
- [policy-description]


## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of Application Gateway.

- [Reliability](/azure/advisor/advisor-reference-reliability-recommendations#application-gateway)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations#make-sure-you-have-enough-instances-in-your-application-gateway-to-support-your-traffic)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#networking)


## Related resources

[Add your content.]



## Next step -or- Related content

> [!div class="nextstepaction"]
> [Next sequential article title](link.md)

-or-

- [Related article title](link.md)
- [Related article title](link.md)
- [Related article title](link.md)


