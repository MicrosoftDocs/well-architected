---
title: Prioritize critical flows and users
description: Learn about recommendations for prioritizing the performance of critical flows and users. Ensure that critical flows get the resources they need before lower-priority flows get those resources.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for prioritizing critical flows 

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:** 

|[PE:09](checklist.md)| Prioritize critical flows. The allocation of workload resources and performance optimization efforts should prioritize the flows that support the most important business processes, users, and operations.| 
|---|---| 

This guide describes the recommendations for prioritizing critical flows in a workload. Critical flows represent crucial business processes that generate revenue or drive high-priority operations. Prioritizing critical flows means ensuring that these flows receive the resources they need before lower-priority flows receive those resources. 

By prioritizing critical flows, you can ensure that less-important flows don't adversely affect critical users and important processes. Failure to prioritize the performance of critical flows can have disproportionate negative effects on workload priorities and the user experience.

**Definitions**

|Term|Definition|
|-|-|
| Flow | In a workload, the sequence of actions that performs a specific function. A flow involves the movement of data and the running of processes between components of the workload. |
|Data flow | The movement of data within a workload.  |
|System flow |The flow of information and processes within a system. The system automatically follows this flow to enable user flows or workload functionality. |
|User flow | The sequence that a user follows to accomplish a task. |
| Rate limiting |The act of limiting how many requests can access a resource.|
| Priority queue processing |The act of processing high-priority tasks before low-priority tasks.|

## Key design strategies

Typically, critical flows significantly affect the user experience or business operations. Critical flows have higher performance targets and service-level agreements than noncritical flows. Where resources are limited, noncritical flows should yield resource usage to critical flows. Align the flow priority with workload business models or connect the priority to business outcomes.

### Identify critical flows

Critical flows refer to the key user flows for paying customers or the flows for operations that are crucial to the workload functionality. These flows can include actions such as user registrations, sign-ins, product purchases, accessing pages behind a paywall, or any other key path or process within your workload. 

To identify critical flows, complete these tasks:

- **Map the flows.** Identify the paths or sequences of user interaction within your workload. Map the dependencies between components or services within your application. Understand how these components interact to support the user flows. Also map the flows and identify the paths or sequences of user interaction within your application. To map the flows, you can use flowcharts, user journey maps, and prototyping tools.

- **Collect data.** Monitor and collect data that's related to the performance of each user flow. Collect metrics such as response times, error rates, throughput, and resource use. You can use these tools to collect data:

    - *Analytic and tracking tools*: These tools provide insights into user behavior and interactions within your application. By analyzing user data, you can identify the most common flows, bottlenecks, or potential issues.
    
    - *Application performance monitoring (APM) tools*: Use APM tools to monitor the performance of your application and track how flows run. These tools provide visibility into response times, errors, and other performance metrics, allowing you to identify critical flows and optimize their performance.
    
    - *Logging and debugging tools*: Use these tools to capture and analyze logs and debug information while your application runs. Review logs and debugging information to trace how flows are running and identify issues or errors.
    
- **Align flow importance with workload objectives.** In your workload, assess the significance of each user flow. Assess the actual usage to determine the flow's significance. Data about flows, queries, and code paths is key to identifying what's critical as you align flows with workload priorities. For example, a free tier might receive more traffic than a paid tier, but the free tier might be less critical than the paid tier.

    Consider factors such as how important the flow is to your business goals, the number of users that rely on the flow, and the potential consequences of any failures or performance issues in the flow. Assess how each flow affects the user experience or business outcomes. Consider factors such as user satisfaction, conversion rates, revenue generation, and customer retention. 

    Analyze the collected performance data to identify patterns or anomalies that might indicate critical flows. Based on your analysis of the performance data, prioritize the user flows that are critical to your workload. These flows significantly affect the user experience or business outcomes. Pay special attention to flows that have high user traffic or flows that directly contribute to revenue generation.
    
    | Critical flows | Noncritical flows
    |-|-|
    | High usage | Low usage |
    | Business critical | Not business critical |
    | Expensive operations | Small operations |
    | Time-sensitive | Not time-sensitive |
    |Production | Preproduction |
    | Real-time processing | Batch processing |
    | Latency sensitive | Not latency sensitive |
    | Paying user | Nonpaying user |
    | Premium tier | Basic tier|
    | Important tasks | Nonessential tasks |
    | High-revenue accounts | Low-revenue accounts |

### Optimize capacity allocation

Where you can't isolate critical flows, prioritize capacity allocations. Understand where your tightest constraints are and optimize capacity allocations where they're needed. For example, queues can process only a certain number of messages per minute, but some storage limits are hard to reach. 

To decide how to allocate capacity:

- *Assess resource capacity*: Evaluate how much resource capacity can be allocated to the flows. Capacity might include resources such as CPU, memory, storage, and network bandwidth. Understand the limitations and constraints of your infrastructure or environment.

- *Analyze flow requirements*: Analyze the resource requirements of each flow. Understand the resources the flow needs to operate efficiently. For each flow, identify the resource demands, such as CPU utilization, memory requirements, and network bandwidth.

- *Prioritize allocations*: Match the available resource capacity to the resource requirements of the flows. Allocate resources based on flow priorities, ensuring that higher-priority flows receive the necessary resources to meet their requirements.

**Use rate limiting.** To ensure that critical flows can consume the resources they need to meet their performance targets, apply rate limits to nonpriority flows and tasks. Rate limits cap the number of requests nonpriority flows and users can make to constrained resources. For example, you might rate-limit nonpriority requests to an API. For more information, see the [Rate Limiting pattern](/azure/architecture/patterns/rate-limiting-pattern) and [Rate limiting an HTTP handler in .NET](/dotnet/core/extensions/http-ratelimiter).

**Use priority queue processing.** Priority queue processing gives high priority to certain requests. Queues usually have a first in, first out (FIFO) structure, but you can update your application to assign a priority to messages it adds to the queue. Use this capability to prioritize critical flows and users. For more information, see the [Priority Queue pattern](/azure/architecture/patterns/priority-queue).

**Isolate critical flows.** Isolating critical flows means providing dedicated resources or capacity to support critical flows. The goal is to ensure critical flows receive enough computing power, network bandwidth, and resources to operate efficiently and effectively. By isolating critical flows, you can more easily manage their resources. Segmentation is a good strategy to isolate critical flows.

- *Resource segmentation*: Create separate resources for critical flows, allowing them to operate independently without interference from other processes. For example, you can isolate critical flows on dedicated network segments or by using dedicated servers to handle the processing needs of these flows. This approach helps minimize how noncritical flows can negatively affect critical flows.

- *Logical segmentation*: Use virtualization and containerization tools like Docker or Kubernetes to isolate flows at the software level. You can separate critical flows into virtual machines (VMs). By doing so, you create an isolated environment, reducing dependencies and potential interference from other flows.

- *Capacity allocation*: For critical flows, explicitly allocate a fixed set of capacity such as CPU, memory, and disk I/O. This allocation ensures that critical flows always have enough resources to operate efficiently. Set resource quotas or limits by using orchestration platforms. By explicitly allocating resources to critical flows, you prevent resource contention and prioritize how they run.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Resource segmentation affects costs. When you dedicate resources to particular flows, you often increase the cost and leave some resources underutilized.

### Find the balance

Balancing the needs of critical flows with the overall performance of a workload is challenging. Although you should prioritize critical flows, you shouldn't neglect noncritical flows. The overall performance efficiency of a workload depends on all flows. Neglected noncritical flows could create issues that affect all users. Too much noise from nonessential items steals attention from critical items. But too little noise could harm the entire workload. The amount of data and the number of alerts should reflect these balanced priorities.

## Azure facilitation

Azure services can help you prioritize critical flows in your workload:

- *Collect metrics*: Azure provides various solutions to help you monitor the performance of critical flows in your workload. Azure Monitor, Azure Monitor Logs, and Azure Application Insights are some of the services that offer comprehensive monitoring capabilities for various types of applications and workloads.

- *Rate-limit*: Some Azure services, such as [Azure API Management](/azure/api-management/rate-limit-by-key-policy), provide built-in policies for rate limiting. Azure provides detailed guidance and a sample implementation of the [Rate Limiting design pattern](/azure/architecture/patterns/rate-limiting-pattern).

- *Use priority queue processing*: Azure Functions provides event-driven functions that you can trigger in various ways, including by a new message in a queue or topic. Combine Azure Functions with Azure Queue Storage or [Azure Service Bus](/azure/architecture/patterns/priority-queue) to process messages based on their priority.

- *Optimize capacity allocations*: Some Azure services support resource segmentation, logical segmentation, and capacity allocation techniques to allocate capacity and resources to critical flows. You can isolate critical flows through techniques such as creating separate resources, increasing density, using virtualization and containerization, and explicitly allocating resources to critical flows.

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