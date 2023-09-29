--- 
title: Recommendations for resource and responsibility consolidation 
description: Get recommendations consolidating workload resources and responsibility. 
author: stephen-sumner 
ms.author: ssumner 
ms.date: 11/15/2023 
ms.topic: conceptual 
--- 

# Recommendations for consolidation: CO 14

This guide describes the recommendations for consolidating workload resources and responsibility to optimize workload costs. Consolidating resources is a nuanced task, distinct from simply eliminating waste.Consolidation involves bringing together various components of a workload, such as servers, databases, applications, and responsibilities. When suitable, consolidation allows workloads to reduce redundant resources & licenses and increase density. You should also look for opportunities to consolidate workload responsibility. Using centralized resources or teams offers other opportunities to optimize workload costs. Without consolidating resources or responsibility, you might miss opportunities for cost savings through shared resources and economies of scale.

**Definitions**

| Term         | Definition                                                                                      |
|---|---|
| Consolidate     | The act of combining to meet requirements with less.                                                                 |
| Resource density   | A measure of logical separation within a resource. Increased density typically equates to higher utilization through co-location of disparate components, consumers, or environments. |
| Centralized resource | A shared resource that multiple components use, rather than each having its own dedicated resource.                                          |
| Change control    | A structured methodology for managing and implementing changes.                                                            |

## Key strategies

The goal of consolidation is not merely to reduce, but to optimize. It requires restructuring workload resources and team responsibilities for maximum cost optimization. Unlike eliminating waste, consolidation isn’t straightforward. Resource consolidation has tradeoffs and risk. It requires an analysis of potential benefits and associated trade-offs.

### Consolidate resources

Consolidating resources is about combining resources within a workload. It involves co-locating different functionalities or consumers into a combined resource. For example, you might consolidate three web servers into a single server or three databases to a single database server. You might use a single load balancer or firewall across multiple environments. The aim is to maximize the cost efficiency of each resource by increasing resource density. Intensify the use of a resource and eliminate any excess.

Common types of services that you can consolidate include application platforms, databases, network appliances, gateways, and DDoS protection. To consolidate resources within a workload, consider these recommendations:

**Assess the workload resources.** Evaluate the current state of the workload, including its performance, resource utilization, and dependencies. Identify any inefficiencies or areas where consolidation could be beneficial.

**Identify consolidation target.** Determine the resource to consolidate on. It can be an existing resource or a new resource created within the workload. When consolidating to an existing resource, identify any existing resources that you use for consolidation. For example, you may have underutilized servers that can accommodate some of the workload components. If no existing resources meet the consolidation requirements or if it's more beneficial to move to a new platform, consider creating a new resource for consolidation.

**Evaluate consolidation viability.** Ensure functional and technical requirements support consolidation such as CPU, memory, growth. Avoid compromising requirements like performance, reliability, and security. For instance, don't create an undesired cross-regional dependency or consolidate resources across pre-production and production environments.

**Estimate the cost.** Understand the effort and potential complications of consolidation. You should calculate costs including resource, licensing, and operational expenses. Consider the implications, such as potential challenges in resource monitoring due to consolidation.

**Communicate and coordinate.** Ensure that all stakeholders are informed about upcoming changes, their effects, and any necessary actions they need to take. Coordinate with different teams involved to avoid conflicts and ensure a smooth implementation.

**Risk:** Consider the effects of resource density, such as noisy neighbors, scale unit effects, and reduced redundancy. Resource consolidation is often too risky for mission and business critical workload flows.

**Tradeoffs:**

- Resource consolidation result in less isolation and could create a noisy neighbor scenarios in workload. Look for additional ways of logical isolation and increased capacity for the hosting environment. For example, firewall capacity needs to be increased if it starts supporting multiple workloads.
- Consolidation eliminates segmentation and can increase security risk, making it easier for attackers to move horizontally. It also makes some compliance standards harder to reach. Always prioritize compliance over consolidation.
- Consolidation of resources results in less redundancy. It requires careful planning to ensure you have the right amount of reliability of the workload.

### Consolidate responsibility

Consolidating workload responsibility is about reducing the set of responsibilities that workload team has. It’s a strategic cost optimization effort that requires organizational awareness and collaboration outside the workload team. There are two principal options. You can use external shared or centralized resources and not manage that resource in the workload environment. You can also offload workload responsibilities to other teams in the organization and not manage the associated tasks or personnel.

#### Use external centralized resources

External centralized resources refer to shared resources that multiple workloads use. The goal is to minimize duplication and overhead. Instead of your workload having a dedicated resource, you use a shared resource to optimize costs.

**Assess the workload resources.** Evaluate the current state of the workload and identify any areas where consolidation could be beneficial.

**Find external opportunities.** Survey your organization to locate any pre-existing centralized resources. These resources could be potential solutions for your workload. For example, instead of setting up an independent security information and event management (SIEM) tool, you could use a centralized SIEM.

**Consider change control.** Familiarize yourself with the process of managing changes to the centralized resource. This includes the approval workflow, testing protocols, and deployment methods. Analyze potential challenges with reduced control over resource modifications.

**Estimate the cost.** Before moving to centralized resources, it's important to clearly quantify the expected savings against the costs associated with such a transition. This will help in making an informed decision. Weigh the cost-saving benefits against these risks to make an informed decision.

**Communicate and coordinate.** Ensure that all stakeholders are informed about upcoming changes, their impact, and any necessary actions they need to take. Coordinate with different teams involved to avoid conflicts and ensure a smooth implementation.

**Document and track changes.** Maintain detailed documentation of all approved changes, including their scope, implementation steps, and any associated risks or issues. Use a centralized system or change management tool to track and monitor the status of changes throughout their lifecycle.

**Tradeoffs:** Over-consolidation can result in resource contention, leading to performance issues. Consolidation may limit the flexibility and agility of individual teams and workloads as they must adhere to centralized standards which could inhibit customization.

#### Offload responsibility to external teams

Centralizing workload responsibilities involves leveraging specialized services provided by expert centralized teams. The goal is to using existing teams to offload responsibility. It not only helps with cost optimization but also ensures expertise in specific functions.

**Evaluate team skills.** Assess the current skill set of your team. Identify any skill gaps or areas where delegating tasks to a centralized team would bring about cost efficiency.

**Find available opportunities.** Explore your organization for available services, such as security operation team. Ensure that the centralized teams can accommodate the added responsibilities without compromising on quality.

**Consider change control.** Familiarize yourself with how the centralized team handles changes—from approval workflows and testing protocols to deployment strategies. Recognize potential challenges that might arise from having less direct control over these functions.

**Communicate and coordinate.** Ensure that all stakeholders are informed about upcoming changes, their impact, and any necessary actions they need to take. Coordinate with different teams involved to avoid conflicts and ensure a smooth transition.

**Document and track changes.** Maintain detailed documentation of all approved changes, including their scope, implementation steps, and any associated risks or issues. Use a centralized system or change management tool to track and monitor the status of changes throughout their lifecycle.

**Onboard.** Ensure that both the workload team and the centralized team undergo proper training sessions. This will familiarize them with each other's processes, tools, and expectations. Consider a phased transition or pilot period to ease the shift and identify potential challenges early.

**Give feedback.** Establish a mechanism for continuous feedback between teams. This helps in addressing concerns, improving collaboration, and refining processes over time.

## Azure facilitation

**Density support.** Many Azure services support increased density. These services include:

|Azure services|Segmentation control|
| --- | --- |
|Azure Front Door|Customer domains and URL paths|
|Azure Firewall|Network and application rules|
|Azure Application Gateway|Listeners, URL path-based routing|
|API Management|API policies|
|Azure Kubernetes Service| Namespace, node pools|
|Azure App Service|Multiple web apps and APIs on an App Service Plan|
|Azure SQL Database|Multiple databases on a server

**Resource observability.** Azure Monitor provides a centralized platform for monitoring and managing the performance and health of your Azure resources. It allows you to collect and analyze telemetry data, set up alerts, and gain insights into resource utilization.

Log Analytics enables centralized log management and analysis. It allows you to collect, analyze, and visualize log data from various Azure resources, helping you identify issues, troubleshoot problems, and gain operational insights.
