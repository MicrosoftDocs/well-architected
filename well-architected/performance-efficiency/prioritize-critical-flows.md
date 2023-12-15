---
title: Recommendations for prioritizing the performance of critical flows
description: Learn about recommendations for prioritizing the performance of critical flows. Ensure that critical flows get the resources they need before lower-priority flows get those resources.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for prioritizing the performance of critical flows

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:**

|[PE:09](checklist.md)| Prioritize the performance of critical flows. The allocation of workload resources and performance optimization efforts should prioritize the flows that support the most important business processes, users, and operations.|
|---|---|

This guide describes the recommendations for prioritizing the performance of critical flows in a workload. Critical flows represent crucial business processes that generate revenue or drive high-priority operations. When you prioritize the performance of critical flows, you ensure the flows that have the most impact get the resources they need before lower priority flows. Failure to do this prioritization can have disproportionate negative effects on workload priorities and the user experience.

**Definitions**

|Term|Definition|
|-|-|
| Flow | In a workload, the sequence of actions that performs a specific function. A flow involves the movement of data and the running of processes between components of the workload. |
| Priority queue processing |The act of processing high-priority tasks before low-priority tasks.|
| Rate limiting |The act of limiting how many requests can access a resource.|
|System flow |The flow of information and processes within a system. The system automatically follows this flow to enable user flows or workload functionality. |
|User flow | The sequence that a user follows to accomplish a task. |

## Key design strategies

Critical flows refer to the key user flows for customers or the system and data flows for operations that are crucial to the workload functionality. These flows can include actions such as user registrations, sign-ins, product purchases, accessing pages behind a paywall, or any other key path or process within your workload.

Critical flows significantly affect the user experience or business operations. Critical flows have higher performance targets and service-level agreements than noncritical flows. Where resources are limited, noncritical flows should yield resource usage to critical flows. You need to identify, monitor, and prioritize all flows before isolating and optimizing critical flows.

### Identify all flows

The first step in prioritizing the performance of critical flows is identifying all the flows within your workload. Flow identification involves systematically mapping and understanding every user paths and component communication. The focus is on understanding the performance metrics and potential impact of flows on workload performance.

By dissecting the workload into discrete flows, you can find performance bottlenecks, inefficient resource utilization, and opportunities for performance optimization. This knowledge exposes areas of needed improvement and is the first step to identifying critical flows. For more information, see [Identify and rate user and system flows](../reliability/identify-flows.md).

### Monitor flow performance metrics

After you identify all flows within your workload, you need to collect performance metrics on each flow and monitor those metrics. Flow metrics provide insights into response times, error rates, and throughput. The goal is to consistently observe and record performance-related metrics to further refine your understanding of each flow's impact on workload performance. To monitor flow metrics, you can use the following tools to collect data:

- *Analytic and tracking tools*: These tools provide insights into user behavior and interactions within your application. By analyzing user data, you can identify the most common flows, bottlenecks, or potential issues.

- *Application performance monitoring (APM) tools*: Use APM tools to monitor the performance of your application and track how flows run. These tools provide visibility into response times, errors, and other performance metrics, allowing you to identify critical flows and optimize their performance.

- *Logging and debugging tools*: Use these tools to capture and analyze logs and debug information while your application runs. Review logs and debugging information to trace how flows are running and identify issues or errors.

### Identify critical flows

With the performance data available, you can begin ranking all the flows and identifying the critical flows. The identification of critical flows involves evaluating the performance impact and criticality of each flow. Effective flow prioritization ensures that the most important flows receive the resources needed before less critical flows. To prioritize flows in your application, consider these steps:

- *Identify business impact*: Start by assessing the importance of each flow within your operations. Focus on how each flow aligns with your business objectives, its impact on users, and the potential negative effects of poor performance. For instance, while a free service tier might attract more users, a paid tier could be more vital for your business goals.

  Additionally, consider the performance impacts of a flow across one or more business processes. Multiple flows might support a single business process, but often, one flow has a significant effect on the performance of that process. You want to identify the flows that the greatest performance impact. Conversely, a single flow might underpin several processes. In such cases, the performance of this flow directly influences the efficacy of all related processes, and it's likely a critical flow.

- *Analyze performance data*: Analyze the performance metrics associated with each flow. Look for patterns, anomalies, or standout metrics that can provide insights into the flow's efficiency and importance. For example, system flows with significant usage are likely important flows.

- *Assign criticality rating*: Based on the business impact and performance indicators, you should prioritize the flows. Use criticality ratings of *High*, *Medium*, and *Low*. Flows with a significant business impact or high performance demand should receive a "High" criticality rating. These flows are your critical flows. Focus on flows with high user traffic or have a direct effect on revenue generation. The following table provides characteristics of critical (*High*) and noncritical flows (*Medium* to *Low*).

| Critical flows | Noncritical flows |
|-|-|
| High usage | Low usage |
| Business critical | Not business critical |
| Expensive operations | Small operations |
| Time-sensitive | Not time-sensitive |
| Production | Preproduction |
| Real-time processing | Batch processing |
| Latency sensitive | Not latency sensitive |
| Paying user | Nonpaying user |
| Premium tier | Basic tier|
| Important tasks | Nonessential tasks |
| High-revenue accounts | Low-revenue accounts |

### Isolate critical flows

The process of isolating critical flows is about providing dedicated resources or capacity to support critical flows. You want to allocate resources and attention to those flows that are essential for optimal user experience or significant business outcomes. The goal is to ensure critical flows receive enough computing power, network bandwidth, and resources to operate efficiently and effectively. By isolating critical flows, you can more easily manage the resources that support critical flows. Here are recommendations for isolating critical flows:

- *Resource segmentation*: Create separate resources for critical flows, allowing them to operate independently without interference from other processes. For example, you can isolate critical flows on dedicated network segments or by using dedicated servers to handle the processing needs of these flows. This approach helps minimize how noncritical flows can negatively affect critical flows.

- *Logical segmentation*: Use virtualization and containerization tools like Docker or Kubernetes to isolate flows at the software level. You can separate critical flows into virtual machines (VMs). By doing so, you create an isolated environment, reducing dependencies and potential interference from other flows.

- *Capacity allocation*: For critical flows, explicitly allocate a fixed set of capacity such as CPU, memory, and disk I/O. This allocation ensures that critical flows always have enough resources to operate efficiently. Set resource quotas or limits by using orchestration platforms. By explicitly allocating resources to critical flows, you prevent resource contention and prioritize how they run.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Resource segmentation affects costs. When you dedicate resources to a flow, you often increase the cost and leave some resources underutilized. To justify the performance enhancements to critical flows, the increase in business impact must outweigh the increase in cost.

### Optimize capacity allocation

When you can't isolate critical flows, the next best option is to prioritize critical flows in accessing available capacity. The optimization of capacity allocation is about strategically distributing available capacity to different flows based on their criticality. Capacity includes CPU, memory, storage, and network bandwidth. The goal is to ensure that the most critical flows (highest priority) receive the necessary capacity to operate effectively. To decide how to allocate capacity, consider these strategies:

- *Assess resource capacity*: Evaluate how much resource capacity can be allocated to the flows. Capacity might include resources such as CPU, memory, storage, and network bandwidth. Understand the limitations and constraints of your infrastructure or environment.

- *Analyze flow requirements*: Analyze the resource requirements of each flow. Understand the resources the flow needs to operate efficiently. For each flow, identify the resource demands, such as CPU utilization, memory requirements, and network bandwidth.

- *Prioritize allocations*: Match the available resource capacity to the resource requirements of the flows. Allocate resources based on flow priorities, ensuring that higher-priority flows receive the necessary resources to meet their requirements. Understand where your tightest constraints are and optimize capacity allocations where they're needed. For example, queues can process only some messages per minute, but some storage limits are hard to reach.
  
- *Use rate limiting*: To ensure that critical flows can consume the resources they need to meet their performance targets, apply rate limits to noncritical flows and tasks. Rate limits cap the number of requests lower-priority flows and users can make to constrained resources. For example, you might rate-limit nonpriority requests to an API. For more information, see the [Rate Limiting pattern](/azure/architecture/patterns/rate-limiting-pattern) and [Rate limiting an HTTP handler in .NET](/dotnet/core/extensions/http-ratelimiter).

- *Use priority queue processing*: Priority queue processing gives high priority to certain requests. Queues usually have a first in, first out (FIFO) structure, but you can update your application to assign a priority to messages it adds to the queue. Use this capability to prioritize critical flows and users. For more information, see the [Priority Queue pattern](/azure/architecture/patterns/priority-queue).

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: It can be a challenge to balance the needs of critical flows with the overall performance of a workload. Although you should prioritize critical flows, you shouldn't neglect noncritical flows. The overall performance efficiency of a workload depends on all flows. Neglected noncritical flows could create issues that affect all users. Too much noise from nonessential items steals attention from critical items. But too little noise could harm the entire workload. The amount of data and the number of alerts should reflect these balanced priorities.

## Azure facilitation

**Identifying and monitoring flows**: Azure provides different solutions to help you monitor the performance of critical flows in your workload. Azure Monitor, Azure Monitor Logs, and Azure Application Insights are some of the services that offer comprehensive monitoring capabilities for several types of applications and workloads.

**Optimizing capacity allocations**: Some Azure services support resource segmentation, logical segmentation, and capacity allocation techniques to allocate capacity and resources to critical flows. You can isolate critical flows through techniques such as creating separate resources, increasing density, using virtualization and containerization, and explicitly allocating resources to critical flows.

Some Azure services, such as [Azure API Management](/azure/api-management/rate-limit-by-key-policy), provide built-in policies for rate limiting. Azure provides detailed guidance and a sample implementation of the [Rate Limiting design pattern](/azure/architecture/patterns/rate-limiting-pattern).

Azure supports priority queue processing. Azure Functions provides event-driven functions that you can trigger in various ways, including by a new message in a queue or topic. Combine Azure Functions with Azure Queue Storage or [Azure Service Bus](/azure/architecture/patterns/priority-queue) to process messages based on their priority.

## Related links

- [Priority Queue pattern](/azure/architecture/patterns/priority-queue)
- [Rate Limiting pattern](/azure/architecture/patterns/rate-limiting-pattern)
- [Rate limiting an HTTP handler in .NET](/dotnet/core/extensions/http-ratelimiter)
- [Azure API Management](/azure/api-management/rate-limit-by-key-policy)
- [Azure Service Bus](/azure/architecture/patterns/priority-queue)


## Performance Efficiency checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
