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
> Foundational architecture that demonstrates the key recommendations: [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).


##### Technology scope

This review focuses on the interrelated decisions for these Azure resources.  

- Azure App Service Plan
- Azure App Service for Web Apps

    Within Web Apps, there's support for Web Apps for Containers, which is out-of-scope for this article. Also, App Service Environment is not included in this guidance.

There are other Azure offerings associated with App Service, such as Functions and Logic Apps. Those offerings are out of scope for this article.


## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the SKUs and features of App Service and their dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Prioritize user flows**. Not all flows are equally critical. Assign priorities to each flow to guide your design decisions. The identified flows significantly influence the overall architecture. For example, your application might include a frontend and backend tiers that communicate through a message broker. Because of a queue in the middle, you may need additional instances on the frontend for optimal performance on the UI side. However, the backend may not require the same number of instances.
>
>   That design will influence the choice of service tiers and instances on the App Service Plan and configurations that allows for independent scaling based on CPU, memory requirements.
>
> - **Anticipate potential failures and have corresponding mitigation strategies**. Here are some examples of failure mode analysis:
>
>   |Failure|Mitigation|
>   |---|---|
>   |Failure of underlying  or abstracted components of App Service.| Have component redundancy in instances and dependencies. Monitor the health of instances, network, and storage performance.|
>   |Failure of external dependencies. | Use design patterns such as retry mechanisms, circuit breakers, and others. Monitor those dependencies and set appropriate timeouts.
>   |Failure due to traffic getting routed to unhealthy instances.| Monitors instance health taking into consideration its CPU usage, memory, responsiveness and avoid sending requests to unhealthy instances. |  
>
>   For more information, see  [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).
>
> - **Build redundancy** in the application and supporting infrastructure. Spread instances across availability zones to improve fault tolerance. If one zone fails, traffic can be routed to other zones. Deploying your app across multiple regions ensures that even if an entire region experiences an outage, your app remains accessible. 
>
>   Build similar level of redundancy in dependent services. For instance,  the application instances bind to blob storage. If the application uses zone-redundant deployment (ZRS), consider configuring the associated storage account with ZRS as well.
> 
>   Have redundancy in networking components. For example, use zone-redundant IP addresses and external load balancers.
>
> - **Have a reliable scaling strategy**. Unexpected load on the application can cause it to become unreliable. Consider the right scaling approach based on your workload characteristics. You might be able to handle the load by scaling up. However, if the load continues to increase, scale out to new instances. Prefer automatic scaling over manual approaches. Always maintain a buffer of extra capacity during scaling operations to prevent performance degradation.  
>
>   The choice of [App Service plan tier has a direct impact on scaling](/azure/app-service/overview-hosting-plans#how-does-my-app-run-and-scale) in terms of number of instances and the compute units. 
> 
>   Ensure proper app initialization, so that new instances are warmed up quickly and can start receiving requests.
>
>   Strive for stateless applications, as much as possible. Reliably scaling state with new instances can add complexity. If you need to store application state, consider an external data store that can be scaled independently.
>
>   Regularly test your autoscaling rules. Simulate load scenarios to verify that your app scales as expected. Also log scaling events.
>
>  - **Plan your recoverability**. Redundancy is a crucial for business continuity. Fail over to another instance if one becomes unreachable. Also, explore auto-healing capabitlites offered by App Service, such as auto repair of instances. 
>
>     Implement design patterns to handle graceful degradation for both transient failures, such as network connectivity issues, and large-scale events like regional outages. For example, 
>     - Bulkhead pattern segments your application into isolated groups to  prevent a failure from affecting the entire system.
>     - Queue-Based Load Leveling pattern queues work items that serve as a buffer to smooth out traffic spikes.
>     - Retry patterns handles transient failures caused by network glitches, dropped database connections, or busy services.
>     - Circuit Breaker pattern to prevent an application from repeatedly trying to execute an operation that's likely to fail.
>    Web Jobs allow you to run background tasks in your Web App. To run those tasks reliably, ensure that the web app hosting your job is set to run continuously, on a schedule, or based on event-driven triggers.
>
>     For more information, see [Reliability patterns](/azure/well-architected/reliability/design-patterns).
>
> - **Conduct reliability testing**. Conduct load testing to evaluate your application's reliability and performance under load. Test plans should include scenarios that validate your automated recovery operations.
>
>   Use fault injection to intentionally introduce failures and validate your self-healing and self-preservation mechanisms. Explore the [fault library provided by Azure Chaos Studio](/azure/chaos-studio/chaos-studio-fault-library). 
>
>    Azure App Service imposes resource limits on hosted apps. These limits are determined by the associated App Service plan. Make sure your tests validate that the app runs within those limits, For more information, [Azure subscription and service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits?branch=main#app-service-limits).
>
> - **Use health probes to identify unresponsive workers**. App Service has built in capabilities that periodically pings a specific path of your web application. If an instance is unresponsive, it's removed the load balancer and replaced with a new instance. 


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


For comprehensive governance, review the [Azure Policy built-in definitions](/azure/app-service/policy-reference) and other policies that might impact the security of the compute layer.


## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your web application instances.

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