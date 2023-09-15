---
title: Recommendations for prioritizing the performance of critical flows and users
description: Learn about recommendations for prioritizing the performance of critical flows and users.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for prioritizing critical flows: PE 09

This guide describes the best practices for prioritizing critical flows in a workload. Critical flows represent crucial business processes that drive revenue generation or high-priority operations. Prioritizing critical flows means ensuring these flows receive the resources they need before lower priority flows. By giving priority to critical flows, you can ensure that critical users and important processes are not adversely impacted by other less important flows. Failure to prioritize the performance of critical flows could have disproportionate negative effects on workload priorities, affecting the workload and user experience.

## Definitions

|Term|Definition|
|-|-|
| Flow | A flow refers to the sequence of actions and interactions that occur within a workload to achieve a specific goal or functionality and involves the movement of data and the execution of various processes between different components of the workload. |
|Data flow | Data flows refer to the movement of data within a system or between different systems.  |
|System flow |The flow of information and processes within a system. |
|User flow | User flows refer to the paths or sequences of actions that users take within an application or system. |
| Rate limiting |Limiting how many requests can access a resource.|
| Priority queue processing |Processing high priority requests before lower priority requests.|

## Key strategies

Critical flows typically have a high impact on the user experience or business operations. The result is higher performance targets and service level agreements than non-critical counterparts. Where resources are limited, non-critical flows should yield resources usage to critical flows. The priority of flows should align with workload business models or tie back to business outcomes.

### Identify critical flows

Critical flows refer to the key user flows for paying customers or operations that are crucial for the functioning of the workload. These flows can include actions such as user registrations, logins, product purchases, accessing pages behind a paywall, or any other key path or processes within your workload. To identify critical flows, follow these steps:

*Map out the flows:* Identify the different paths or sequences of user interaction within your workload. Map the dependencies between different components or services within your application. Understand how these components interact with each other to support the user flows. To map out the flows and identify different paths or sequences of user interaction within your application, you can use various tools and techniques. You can use flowcharts, user journey maps, and prototyping tools to map the flows.

*Collect data:* Monitor and collect data related to the performance of each user flow. You should collect metrics such as response times, error rates, throughput, and resource utilization. Here are some tools to collect data:

- *Analytic and tracking tools:* Analytic and tracking tools provide insights into user behavior and interactions within your application. By analyzing user data, you can identify the most common flows and paths that users take, as well as any bottlenecks or issues that may arise.
- *Application performance monitoring (APM) tools:* APM tools help you monitor the performance of your application and track the execution of different flows. These tools provide visibility into response times, errors, and other performance metrics, allowing you to identify critical flows and optimize their performance.
- *Logging and debugging tools:* Logging and debugging tools allow you to capture and analyze logs and debug information during the execution of your application. By reviewing logs and debugging information, you can trace the execution path of different flows and identify any issues or errors.

*Align flow importance with workload objectives:* Assess the significance of each user flow to your workload. Assessing what is critical should rely on actual usage. Data on flows, queries, and code paths are key to identifying what is critical while aligning with workload priorities. For example, free tiers might receive more traffic than paid tiers, but the free tier might not be more critical than paid tiers.

Consider factors such as the importance of the flow to your business goals, the number of users that rely on it, and the potential consequences of any failures or performance issues in that flow. Assess the impact of each flow on the user experience or business outcomes. Consider factors such as user satisfaction, conversion rates, revenue generation, and customer retention. Analyze the collected performance data to patterns or anomalies that may indicate critical flows. Based on the analysis of the performance data, prioritize the user flows that are critical to your workload. These are the flows that have a significant impact on the user experience or business outcomes. Pay special attention to flows with high user traffic or those that directly contribute to revenue generation.

| Critical flows | Non-critical flows
|-|-|
| High usage | Low usage |
| Business critical | Non-business critical |
| Expensive operations | Small operations |
| Time sensitive | Not time sensitive |
|Production | Pre-production |
| Realtime processing | Batch processing |
| Latency sensitive | Not latency sensitive |
| Paying user | Non-paying user |
| Premium tier | Basic-tier|
| Important tasks | Non-essential tasks |
| High-revenue account | Low-revenue accounts |

### Optimize capacity allocation

Where you can't isolate critical flows, you need to prioritize capacity allocations. Understand where your tightest constraints are and optimize capacity allocates where it's needed. For example, queues can only process a certain number of messages per minute, but some storage limits are much harder to reach. Make capacity allocations where they're needed.

- *Assess resource capacity:* Evaluate the available resource capacity, such as CPU, memory, storage, and network bandwidth, that can be allocated to the flows. Understand the limitations and constraints of your infrastructure or environment.
- *Analyze flow requirements:* Analyze the resource requirements of each flow. You should know the resources the flow needs to operate efficiently. Identify the specific resource demands of each flow, such as CPU utilization, memory requirements, or network bandwidth.
- *Align allocation to priority:* Match the available resource capacity to the resource requirements of the flows. Allocate resources based on flow priorities, ensuring that higher priority flows receive the necessary resources to meet their requirements.

**Use rate limiting.** Critical flows should be able to consume the resources they need to meet their performance targets. You can apply rate limits to non-priority flows and tasks. Rate limits cap the number of requests non-priority flows and users can make to constrained resources. For example, you might rate limit non-priority requests to an API. For more information, see [Rate limiting pattern](/azure/architecture/patterns/rate-limiting-pattern) and [Rate limiting an HTTP handler in .NET.](/dotnet/core/extensions/http-ratelimiter)

**Use priority queue processing.** Priority queue processing gives higher priority certain requests. Queues usually have a first in, first out (FIFO) structure. You can update your application to assign a priority to messages it adds to the queue. You should prioritize critical flows and users. For more information, see [Priority Queue pattern](/azure/architecture/patterns/priority-queue).

**Isolate critical flows.** Isolating critical flows means providing dedicated resources or capacity to support critical flows. The goal is to ensure critical flows receive the necessary computing power, network bandwidth, and resources to operate efficiently and effectively. Isolating critical flows can make it easier to manage their resources. Flows use and consume workload resources. Segmentation is a good strategy to isolate critical flows.

- *Resource segmentation:* Create separate resources for critical flows, allowing them to operate independently without interference from other processes. For example, you can isolate critical flows on dedicated network segments or using dedicated servers to handle the processing needs of these flows. This approach helps to minimize the potential impact of non-critical flows on critical flows.
- *Logical segmentation:* Using virtualization and containerization techniques like Docker or Kubernetes, you can isolate flows at the software level. You can separate critical flows into separate virtual machines (VMs). By doing so, you create an isolated environment for critical flows, reducing dependencies and potential interference from other flows.
- *Capacity allocation:* Allocating a fixed set of capacity such as CPU, memory, and disk I/O explicitly to critical flows ensures that they always have the necessary resources to operate efficiently. Set resource quotas or limits using orchestration platforms. By explicitly allocating resources to critical flows, you prevent resource contention and prioritize their execution.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Resource segmentation has cost implications. Dedicating resources to particular flows often increases cost and may increase underutilization.

### Find the balance

Balancing the needs of critical flows with the overall performance of a workload is challenging. It's important to prioritize critical flows, but you shouldn't neglect non-critical flows. The overall performance efficiency of a workload depends on all flows. Neglecting non-critical flows could create issues that affect all users. Too much noise from non-essential items steals attention from critical ones. Too little noise, and it could harm the entire workload. The amount of data and number of alerts should reflect these balanced priorities.

## Azure facilitation

**Collecting metrics.** Use tools like Azure Monitor to gather this data.

**Rate limiting.** Some Azure services provide built-in policies for rate limiting such as [Azure API Management](/azure/api-management/rate-limit-by-key-policy).

-   [Azure Service Bus](/azure/architecture/patterns/priority-queue) has features that support priority queue processing.

-   [Azure Front Door](/azure/frontdoor/routing-methods#priority) and [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-configure-priority-routing-method) allow you to distribute and route traffic based on priority.

## Tradeoff

-   Prioritizing flows and users adds complexity to workload management. The added complexity could affect operational efficiency.

-   Prioritizing flows and users might requires resource isolation and duplication to implement. For example, a priority queue instance and a general queue instance. There's added cost for this duplication
