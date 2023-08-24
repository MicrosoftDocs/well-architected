---
title: Recommendations for prioritizing the performance of critical flows and users
description: Learn about recommendations for prioritizing the performance of critical flows and users.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for prioritizing the performance of critical flows and users: PE 09

This guide describes the best practices for prioritizing the performance of critical flows and critical users. Critical flows and users often represent crucial business processes or high-priority operations. Prioritizing critical flows and critical users means ensuring these receive the resources they need. When under pressure, low priority tasks should yield to high priority ones. These flows and users could align with workload business models or tie back to business outcomes. Failure to prioritize the performance of critical flows and users could have disproportionate negative effects on workload priorities.

## Definitions

|  Term                                |Definition|
|-|-| 
|  Rate limiting |                      Limiting how many requests can access a resource.|
| Priority queue processing           |Processing high priority requests before lower priority requests.|

## Key design strategy

**Identify critical flows and critical users.** Critical flows are key processes or transactions within a workload that are crucial for business. Critical users are more important to business than other users. Critical flows and users typically have higher performance targets and service level agreements than non-critical counterparts. Where resources are limited, non-critical flows should yield resources usage to critical flows.

Assessing what is critical should rely on actual usage. Data on flows, queries, and code paths are key to identifying what is critical while aligning with workload priorities. For example, free tiers might receive more traffic than paid tiers, but the free tier might not be more critical than paid tiers. Examples of critical flows and users includes:


| Critical flows vs non-critical flows           | Critical vs. non-critical users            |
|-|-|
| - High usage vs. low usage  <br>- Business critical vs. non-business critical  <br>- Expensive operations vs. small operations <br>- Time sensitive vs. unbound  <br>- Production vs. pre-production <br>- Realtime vs. batch processing <br>- Latency sensitive vs. non-sensitive |- Paying vs. non-paying  <br>- Premium tier vs. basic-tier<br>- Important tasks vs. non-essential tasks <br>- High revenue vs. low revenue accounts |

**Understand your constraints.** Constraints are limitations. Your personnel and workload have constraints. Your personnel have time and skill constraints. They only have so many hours in a day and only know certain things. The components in your workload have constraints. For example, queues can only process a certain number of messages per minute. However, some component constraints are hard to reach. For example, cloud object storage capacity is much harder to reach. You need to understand where your tightest constraints are and allocate them to the highest priority tasks.

**Isolate critical flows.** Isolate critical flows and users can make it easier to manage their resources. Resource segmentation and tagging can help monitor and prioritize critical flows while not neglecting others. You should evaluate the pros and cons of isolation and implement as needed.

**Use rate limiting.** Critical flows and users should be able to consume the resources they need to meet their performance targets. You can apply rate limits to non-priority flows and tasks. Rate limits cap the number of requests non-priority flows and users can make to constrained resources. For example, you might rate limit non-priority requests to an API. For more information, see [Rate limiting pattern](/azure/architecture/patterns/rate-limiting-pattern) and [Rate limiting an HTTP handler in .NET.](/dotnet/core/extensions/http-ratelimiter)

**Use priority queue processing.** Priority queue processing gives higher priority certain requests. Queues usually have a first in, first out structure. You can update your application to assign a priority to messages it adds to the queue. You should prioritize critical flows and users. For more information, see [Priority Queue pattern.](/azure/architecture/patterns/priority-queue)

**Find the balance.** Balancing the needs of critical users and critical flows with the overall performance of a workload is a challenging task. It's important to prioritize critical flows and users, but you shouldn't overlook non-critical ones. The overall performance efficiency of a workload depends on all flows. Neglecting non-critical flows could create issues that affect all users.

Where resources are limited, you should focus on critical flows and critical users. The amount of data and number of alerts should reflect these balanced priorities. Too much noise from non-essential items steals attention from critical ones. Too little noise, and it could harm the entire workload.

## Azure facilitation

-   Some Azure services provide built-in policies for rate limiting such as [Azure API Management](/azure/api-management/rate-limit-by-key-policy).

-   [Azure Service Bus](/azure/architecture/patterns/priority-queue) has features that support priority queue processing.

-   [Azure Front Door](/azure/frontdoor/routing-methods#priority) and [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-configure-priority-routing-method) allow you to distribute and route traffic based on priority.

## Tradeoff

-   Prioritizing flows and users adds complexity to workload management. The added complexity could affect operational efficiency.

-   Prioritizing flows and users might requires resource isolation and duplication to implement. For example, a priority queue instance and a general queue instance. There's added cost for this duplication
