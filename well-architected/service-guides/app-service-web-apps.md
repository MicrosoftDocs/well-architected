---
title: Azure Well-Architected Framework - App Service (Web Apps)
description: Design considerations and recommendations about App Service (Web Apps).
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 02/25/2024
ms.topic: conceptual
products:
  - azure-app-service
categories:
  - compute
---

# Azure Well-Architected Framework perspective on App Service (Web Apps)

Azure App Service is a type of Platform-as-a-Service (PaaS) compute service that allows you to host your workload on the Azure platform. It's a fully managed service that abstracts the underlying virtual machines and offloads the responsibility of building, deploying, and scaling to the platform. An app service always runs in an App Service plan. The choice of plan defines the region in which the workload runs, the type of operating system, and virtual machines configurations, with various billing models.

This article assumes that as an architect, you've reviewed the [**compute decision tree**](/azure/architecture/guide/technology-choices/compute-decision-tree) and chosen App Service as the compute to run your workload. 

> [!IMPORTANT]
>
> **How to use this guide**
>
> The guidance in this article provides architectural recommendations that are mapped to the principles of the [**Azure Well-Architected Framework pillars**](../pillars.md).
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure App Service for Web Apps and their dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demostrates the key recommendations: [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).


##### Technology scope

This review focuses on the interrelated decisions for these Azure resources.  

- Azure App Service Plan
- Azure App Service for Web Apps

    Within Web Apps, there's support for Web Apps for Containers, which is out-of-scope for this article. 

There are other Azure offerings associated with App Service, such as Functions and Logic Apps. Those offerings are out of scope for this article.


## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the SKUs and features of App Service and their dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|||


## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on App Service.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|||

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to your web app and the environment in which they run.

##### Design checklist

Start your design strategy based on the [**design review checklist for Cost Optimization**](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|||


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [**design review checklist for Operational Excellence**](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to web apps.

> [!div class="checklist"]
>

|Recommendation|Benefit|
|------------------------------|-----------|
|||

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [**design review checklist for Performance Efficiency**](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for web apps.

> [!div class="checklist"]
>

|Recommendation|Benefit|
|------------------------------|-----------|
|||

## Azure policies

Azure provides an extensive set of built-in policies related to App Service and the dependencies. Some of the preceding recommendations can be audited through Azure Policies. For example, you can check if:


- 


For comprehensive governance, review the App Services section in [Azure Policy built-in definitions](/azure/governance/policy/samples/built-in-policies) and other policies that might impact the security of the compute layer.


## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Virtual Machines.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)


## Next steps

Consider these articles as resources that demonstrate the  recommendations highlighted in this article.

- Use these reference architectures as examples of how these recommendations can be applied to a workload.
  - If you have never deployed a Web Apps, start with [Basic web application](/azure/architecture/web-apps/app-service/architectures/basic-web-app).
  - Foundational architecture as your starting point for a production-grade deployment is described in [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).
- Build implementation expertise using product documentation: 
  - [Azure App Service](/azure/app-service/)
  - [Azure App Service plan](/azure/app-service/overview-hosting-plans)