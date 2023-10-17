--- 
title: Recommendations for consolidation 
description: Learn how to use consolidation to restructure your workloads, resources, and team roles to achieve maximum cost efficiency.
author: stephen-sumner 
ms.author: ssumner 
ms.date: 11/15/2023 
ms.topic: conceptual 
--- 

# Recommendations for consolidation

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:14](checklist.md)| Consolidate resources and responsibility. In a workload, determine ways to consolidate resources and increase density. Outside a workload, use existing centralized resources and services, so you can consolidate workload responsibilities. |
|---|---|

This guide describes the recommendations for consolidating resources and responsibilities to optimize workload costs. Consolidating resources is a nuanced task that differs from simply eliminating waste. Consolidation involves combining components of a workload, such as servers, databases, applications, and responsibilities.

Consolidation can reduce redundant resources and licenses, and increase density. Look for opportunities to consolidate your workload responsibilities. Use centralized resources or teams to optimize costs. If you don't consolidate resources and responsibilities by using shared resources and optimizing economies of scale, you might miss opportunities for cost savings.

**Definitions**

| Term | Definition |
|---|---|
| Centralized resource | A shared resource that multiple components use, rather than each component having its own dedicated resource. |
| Change control | A structured methodology for managing and implementing changes.  |
| Consolidate | The act of combining components to optimally meet workload requirements.|
| Resource density  | A measure of logical separation within a resource. Increased density typically equates to higher utilization due to the collocation of disparate components, consumers, or environments. |

## Key design strategies

The primary objective of consolidation is optimization, not reduction. Consolidation involves restructuring workloads, resources, and team roles to achieve maximum cost efficiency. Unlike [eliminating workload waste](eliminate-waste.md), consolidation is a process that requires careful consideration.

Almost every consolidation effort has tradeoffs and potential risks but can significantly reduce costs. It's important to analyze the potential benefits and associated tradeoffs. All consolidation strategies follow these steps:

1. *Assessment*: Perform a thorough evaluation to identify areas where consolidation might be advantageous.

1. *Identification and evaluation*: Pinpoint and assess potential consolidation targets to determine if potential cost benefits and tradeoffs justify the effort of consolidating.

1. *Communication and implementation*: If you determine that consolidation is beneficial, announce the impending changes and apply them.

### Consolidate resources

Consolidating resources involves combining resources within a workload. You can colocate functionalities or consumers into a combined resource. For example, you might consolidate three web servers into a single server or three databases into a single database server. You might consolidate multiple firewalls into a single firewall that serves multiple environments.

The aim is to increase resource density, so you can maximize the cost efficiency of each resource. Expand the use of a resource and minimize resource redundancy.

Common types of services that you can consolidate include application platforms, databases, network appliances, gateways, and distributed denial-of-service (DDoS) protection. To consolidate resources within a workload, consider the following recommendations.

**Assess the workload resources.** Assess the existing workload and its resource utilization. Analyze factors such as CPU usage, memory usage, storage capacity, and network bandwidth. Identify areas in which consolidation might be beneficial. Consolidation might involve optimizing resource allocation, eliminating redundant or underutilized resources, or reconfiguring the workload to run more efficiently. Consider factors such as workload dependencies, performance requirements, and scalability.

**Identify a consolidation target.** Choose a resource to consolidate. It can be an existing resource or a new resource that's created within the workload. Identify existing resources that you might use for consolidation. For example, you might have underutilized servers that can accommodate some of the workload components. If no existing resources meet the consolidation requirements or if it's more beneficial to consolidate to a new resource, consider creating a new resource.

**Evaluate the consolidation viability.** Ensure that functional and technical requirements, such as CPU, memory, and growth, support consolidation. Avoid compromising requirements like performance, reliability, and security. For example, don't create an undesired cross-regional dependency or consolidate resources across preproduction and production environments.

**Estimate the cost.** Determine the effort and potential complications of consolidation. You should calculate costs, including resource, licensing, and operational expenses. Consider the implications, such as potential challenges in resource monitoring due to consolidation.

**Communicate and coordinate with your team.** Ensure that you inform all stakeholders about upcoming changes and necessary actions that they need to take. Coordinate with teams to avoid conflicts and ensure a smooth implementation.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Consider the effects of resource density, such as noisy neighbors, scale-unit effects, and reduced redundancy. Resource consolidation is often too risky for mission-critical and business-critical workload flows.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoffs**:
>
> - Resource consolidation results in less isolation and can create a noisy neighbor scenario in a workload. Find other ways to implement logical isolation and increased capacity for the hosting environment. For example, increase firewall capacity if it supports multiple workloads.
>
> - Consolidation eliminates segmentation and can increase security risk, which makes it easier for attackers to move horizontally. It also makes some compliance standards hard to achieve. Prioritize compliance over consolidation.
>
> - Resource consolidation results in less redundancy. Carefully plan to ensure that you have the proper amount of reliability in the workload.

### Consolidate responsibilities

The goal of consolidating workload responsibilities is to reduce the workload team's responsibilities. It’s a strategic cost optimization effort that requires organizational awareness and collaboration outside the workload team.

There are two principal ways to consolidate your workload team's responsibilities. You can use external shared or centralized resources and not run that resource in the workload environment. You can also offload workload responsibilities to other teams in your organization, so your team isn't directly responsible for those tasks or personnel.

#### Use external centralized resources

External centralized resources refer to shared resources outside the workload environment. For example, an organization might have a centralized gateway that serves multiple workloads. The goal of external centralized resources is to minimize duplication and overhead. Instead of having a dedicated resource for your workload, you can use a shared resource to optimize costs. Consider the following recommendations:

- *Assess the workload resources*. Evaluate the current state of the workload, and identify areas in which consolidation might be beneficial.

- *Find external opportunities*. Survey your organization for pre-existing centralized resources. These resources might be potential solutions for your workload. For example, instead of setting up an independent security information and event management (SIEM) tool, you can use a shared SIEM.

- *Consider change control*. Understand the process of managing changes to the centralized resource. Consider the approval workflow, testing protocols, and deployment methods. Analyze potential challenges if you have reduced control of resource modifications.

- *Estimate the cost*. Before you implement centralized resources, clearly quantify the expected savings against the costs that are associated with a transition. Weigh the cost-saving benefits against risks to make an informed decision.

- *Communicate and coordinate with your team*. Establish a mechanism for continuous feedback among teams to address concerns, improve collaboration, and refine processes.

- *Document and track changes*. Maintain detailed documentation of all approved changes, including their scope, implementation steps, and associated risks or issues. Use a centralized system or change-management tool to track and monitor the status of changes throughout their lifecycle.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Over-consolidation can result in resource contention, which can lead to performance issues. Consolidation might limit the flexibility and agility of individual teams and workloads because they must adhere to centralized standards that can inhibit customization.

#### Offload responsibilities to external teams

Offloading workload responsibilities to external teams refers to using expert centralized teams, such as a security operations team, that perform specialized services. You can offload responsibilities to existing teams to help optimize costs and delegate expertise for specific functions.

- *Evaluate team skills*. Assess the current skill set of your team. Identify skill gaps or areas in which delegating tasks to a centralized team optimizes costs.

- *Find available opportunities*. Explore your organization for available services, such as the services of a security operations team. Ensure that the centralized team can accommodate the added responsibilities without compromising quality.

- *Consider change control*. Familiarize yourself with how the centralized team handles changes, like approval workflows, testing protocols, and deployment strategies. Determine potential challenges that might arise if you have less direct control of these functions.

- *Communicate and coordinate with your team*. Ensure that teams are familiar with each other's processes, tools, and expectations. Consider a phased transition or pilot period to ease the shift and identify potential challenges early.

- *Document and track changes*. Maintain detailed documentation of all approved changes, including their scope, implementation steps, and associated risks or issues. Use a centralized system or change-management tool to track and monitor the status of changes throughout their lifecycle.

## Azure facilitation

**Density support**: Many Azure services support increased resource density. The following table shows these services.

|Azure service|Segmentation control|
| --- | --- |
|Azure Front Door|Customer domains and URL paths|
|Azure Firewall|Network and application rules|
|Azure Application Gateway|Listeners, URL path-based routing|
|API Management|API policies|
|Azure Kubernetes Service (AKS)| Namespace, node pools|
|Azure App Service|Multiple web apps and APIs on an App Service plan|
|Azure SQL Database|Multiple databases on a server

**Resource observability**: Azure Monitor provides a centralized platform for monitoring and managing the performance and health of your Azure resources. You can collect and analyze telemetry data, set up alerts, and gain insight into resource utilization.

Log Analytics provides centralized log management and analysis. You can collect, analyze, and visualize log data from various Azure resources, which helps to identify issues, troubleshoot problems, and gain operational insight.

## Related links

[Recommendations for eliminating workload waste](eliminate-waste.md)

## Cost Optimization checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
