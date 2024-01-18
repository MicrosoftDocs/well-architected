---
title: Recommendations for optimizing flow costs
description: Learn about Azure Well-Architected Framework recommendations for strategically optimizing the cost of each of your workload's flows.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing flow costs

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:09](checklist.md)| Optimize flow costs. Align the cost of each flow with flow priority. When you prioritize flows, consider the features, functionality, and nonfunctional requirements of each flow. Optimizing flow spend often requires strategic compromises.|
|-|-|

This guide describes the recommendations for optimizing the cost of each of the flows in your workload. Cost-optimizing the flows in a workload involves strategically allocating and managing resources to minimize expenses while maintaining performance. This optimization is crucial because it ensures efficient utilization of invested resources, reduces unnecessary expenditures, and improves the overall return on investment for the infrastructure. If you don't cost-optimize the flows in a workload, you risk overspending on resources, which results in inflated operational costs and diminished profitability.

**Definitions**

| Term    | Definition |
|---|----|
|Decouple  | The strategy of removing a flow from a resource that contains multiple flows and placing it into a separate resource. |
|Flow    | In a workload, the sequence of actions that performs a specific function. A flow involves the movement of data and the running of processes between components of the workload. |
|System flow | The flow of information and processes within a system. The system automatically follows this flow to enable user flows or workload functionality.                            |
|User flow  | The paths or sequences of actions that users take within an application or system.|

## Key design strategies

Invest more in high-priority flows than in lower-priority flows. Aligning flow priority and spending can involve decoupling flows that currently share the same resource. It can also involve combining flows that have similar requirements but are run on separate resources. For example, suppose you have a web application that includes multiple flows, like user registration, sign-in, and data processing. These flows run on a single server, even though they have different resource needs. To optimize both costs and performance, you might separate flows or combine flows:

- *Separate flows*. For example, you might decouple the user registration flow from the others and move it to a dedicated, lower-cost server. This flow is important but not resource-intensive, so it's a good candidate for a less expensive server.

- *Combine flows*. For example, you might combine the sign-in and data processing flows, which both have higher resource requirements, and run them together on a high-performance server. Combining these flows enables the server to efficiently handle the resource-intensive needs of both flows. It optimizes performance and costs.

In a workload, there can be different types of flows or paths that you need to consider. This guide focuses on the following flow types:

- *System flows*. Optimizing system flows involves streamlining the communication and interaction between system components, minimizing bottlenecks, and ensuring efficient resource utilization.

- *User flows*. Optimizing user flows involves improving the user experience, reducing friction points, and ensuring smooth navigation and interaction within the application or system.

### Create an inventory of flows

A flow inventory is a comprehensive list and description of all the sequences of actions, data transitions, and system interactions within a workload. A flow inventory is the first step to ensuring investments align with the priority of flows. You should only optimize flows when you fully understand their purpose and dependencies. Here are steps for creating an inventory of workload flows:

1. *Document flows*. Start by documenting and listing all existing flows in your workload to get an understanding of the comprehensive state of the system. Include every sequence of actions, data transitions, and system interactions. Familiarize yourself with every component, like external services, databases, middleware, and third-party integrations. Additionally, track or estimate the volume of requests over time.

2. *Visualize flows*. To get a clearer perspective, represent your findings visually, possibly in flowcharts or diagrams. Visualizations help you see the interdependencies between components. Consider using a tool like Visio to help you with the visualizations.

3. *Categorize flows*. Bundle similar flows, taking into account attributes like their functionality (for example, authentication, data retrieval, and transaction processing), criticality to business, or the resources they use (CPU, memory, or bandwidth).

### Prioritize flows

Flow prioritization is the process of classifying flows based on their influence on business outcomes, implications on user experience, and the resources they consume. Critical flows often require higher levels of availability, faster recovery times, and better performance to meet workload objectives. By prioritizing flows, you can better align spending to flow priority. To prioritize flows, consider the following steps:

- *Identify flow value*. When you optimize workload flow costs, you need to identify the flow that provides the most value. You don't want to spend more than a flow is worth. Instead of simply cutting costs, consider shifting costs to prioritize the more valuable flows. For example, your checkout flow is critical for business, but the purchase history isn't. You should allocate more resources and budget to the checkout flow.

  Low-priority flows have lower expectations for availability, recovery, and performance. You can reduce costs by using cheaper configurations to reduce performance, availability, or business continuity spending.

- *Consider flow metrics*. If you're struggling to prioritize your flows, consider the availability and recovery goals that you assigned to them. Critical flows often have high availability requirements and service-level agreements (SLAs). Flows associated with a lower RPO and RTO are more important than flows that have a higher RPO and RTO.

### Optimize independent flows

Sometimes your flows are already running on different resources. In these cases, you can more easily evaluate and optimize spending. Evaluate the components and processes involved in each independent flow to determine whether there are ways to optimize or simplify them. To optimize independent flows, you can follow these steps:

- *Eliminate unnecessary components*. Remove any extraneous elements that don't contribute to the flow's core functionality, thereby reducing complexity and cost.

- *Redesign the flow*. Consider redesigning the flow's architecture to enhance its efficiency. You might change the sequence of operations, reduce latency, or improve data transfer speeds, for example.

- *Choose an appropriate performance tier*. Different flows might have varying demands in terms of processing speed, memory, or other resource metrics. Make sure to choose a resource tier that aligns well with each flow's specific requirements.

- *Adjust scaling settings*. If a flow experiences variable demand, consider implementing autoscaling to dynamically adjust resources according to real-time needs, thus optimizing costs.

- *Fine-tune configurations*. Fine-tune other settings, like networking or data storage options, to better align with the flow's performance and budget requirements.

### Separate dissimilar flows

Separating dissimilar flows onto different resources is a process of allocating distinct tasks with varying computational needs to dedicated resources. Dissimilar flows are flows that have different attributes. These attributes can include computational requirements, data dependencies, I/O operations, latency sensitivity, security needs, and compliance requirements. It's often more cost efficient to run different types of flows on separate resources. Doing so enables precise resource allocation to each flow, which reduces unnecessary expenditures and ensures maximum efficiency.

Consider separating dissimilar flows that are currently combined. This separation boosts scalability, fault tolerance, and adaptability and also streamlines costs. By ensuring that each flow operates independently, you reduce interference risks and can allocate resources more cost-effectively based on each flow's priority. For example, assume that you colocate CRM (user flow) with a data engine (data flow). User traffic to the CRM system during office hours might slow down the data engine. When you decouple flows, the data engine can scale each component or service independently based on workload demand. This decoupling optimizes resource allocation and reduces costs.

### Combine similar flows

Combining similar flows onto a single resource is a process of consolidating tasks or processes with comparable attributes and using shared resources for them. This strategy eliminates redundancies and ensures more efficient use of resources, leading to significant cost savings. Similar types of flows share similar attributes. You might consider the same attributes that you look at when you separate dissimilar flows: computational requirements, data dependencies, I/O operations, latency sensitivity, security needs, and compliance requirements. Here are some examples where combining similar workload flows to use the same resource can lead to substantial savings:

- *Web servers*. Instead of dedicating separate web servers for each application, consider consolidating them, especially if their traffic isn't consistently high. A shared web server, paired with a reverse proxy, can effectively manage and route traffic to multiple applications.

- *API gateways*. Rather than maintaining individual API gateways for separate microservices or applications, you can use a centralized API gateway to streamline requests and direct them to the relevant service. Doing so makes management easier and also reduces costs.

- *Log processing*. Instead of having multiple applications or services that each operate their own log processing instances, consider directing them all to a shared log processing tool. This approach minimizes the number of active instances, which translates to direct cost savings.

- *Authentication services*. If multiple applications deploy their own distinct authentication mechanisms, redundancy is introduced. Integrating a single sign-on (SSO) solution or a communal authentication service reduces this duplication and optimizes resource usage, which reduces costs.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Don't mistake coincidence with design. Two flows that look similar don't necessarily serve the same purpose. You need to understand the function and design of each flow before merging or changing them. Misinterpreting a flow by focusing solely on its appearance can lead to unintended consequences and disrupt the service or process that it supports. If multiple flows serve the same function and there are no discernible differences in their design or intent, consider consolidating them.

### Continuously monitor flows

The nature of flows and workloads can change over time, so you need to review flow spending to ensure that costs align with priorities. Evaluate the resource utilization of each flow by analyzing the compute, storage, and network usage associated with each flow. Identify any inefficiencies or areas where resources are underutilized. This analysis helps you pinpoint opportunities for cost optimization. Here are some considerations to take into account when you review flow utilization:

- *Analyze usage patterns*. Analyze the usage patterns of the flows. Some flows might be more active during certain times of the day or month, while others might have a consistent load. By understanding these patterns, you can predict resource needs and adjust allocation to avoid bottlenecks and overprovisioning.

- *Monitor relevant metrics*. Determine the metrics that can help you assess the efficiency and cost-effectiveness of each flow. Consider CPU utilization, data transfer costs, transaction costs, and storage footprint. Use monitoring tools to gather detailed metrics about resource usage and performance.

- *Consider ongoing maintenance*. Consider the cost of maintenance, especially when you use infrastructure-as-a-service solutions like virtual machines. You need to account for activities like patching, upgrades, backups, monitoring, and security.

During your analysis, identify any inefficiencies or areas where resources aren't utilized effectively. Consider idle compute instances, unused data, and low network bandwidth. These inefficiencies can indicate opportunities for cost optimization.

## Azure facilitation

**Prioritizing, optimizing, and monitoring flows:** The [User Flow tool](/azure/azure-monitor/app/usage-flows) in Application Insights provides a visual representation of user navigation across your site’s pages and features. This tool aids in identifying areas where users frequently leave, repeat actions, or follow specific paths. By comparing actual user behavior with your anticipated outcomes and objectives, you can identify critical flows. It also allows you to optimize potential issues such as high churn rates, repetitive actions, or design flaws. The tool also allows for custom property filtering through dimensions, offering a more tailored analysis.

[Azure Monitor](https://azure.microsoft.com/products/monitor) helps you gain insights into the performance and health of your applications. It provides monitoring and diagnostics capabilities. These capabilities enable you to identify performance bottlenecks, optimize resource utilization, and detect and troubleshoot issues that might affect costs.

[Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) is a tool that enables you to collect, analyze, and visualize log data from various sources. By using Log Analytics, you can gain insights into your application and infrastructure logs, identify trends, and optimize costs by managing usage and data retention. Consider colocating logs and using dedicated solutions instead of shared ones to better manage costs.

## Related links

- [User Flow tool](/azure/azure-monitor/app/usage-flows)
- [Azure Monitor](https://azure.microsoft.com/products/monitor)

## Cost Optimization checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
