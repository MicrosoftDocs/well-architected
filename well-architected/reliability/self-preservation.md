---
title: Recommendations for designing for self-healing and self-preservation
description: Learn how to build self-healing and self-preservation capabilities into your application architecture to optimize reliability.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
ms.custom:
  - guide
categories:
  - security
---
# Recommendations for designing for self-healing and self-preservation

## Applies to RE:06

This guide describes the best practices for building self-healing and self-preservation capabilities into your application architecture to optimize reliability. Self-preservation capabilities add resilience to your workload, reducing the likelihood of a full outage and affording you the ability to operate in a degraded state while recovering failed components. Self-healing capabilities help you avoid downtime by building in failure detection and automatic corrective actions to respond to many different failure types. This guide describes design patterns focusing on self-preservation and self-healing that you can incorporate into your workload design to strengthen its resiliency and recoverability. Avoiding the use of the patterns puts your apps at risk of failure when inevitable issues arise.

## Definitions

| Term | Definition |
|------|------------|
| Self-healing | Designing your workload to recover impacted components and failover to redundant infrastructure automatically |
| Self-preservation |  Designing your workload to be resilient against potential impacts |

## Key design strategies

### Self-preservation guidance

Designing your workload for self-preservation means following infrastructure and application architecture design patterns to optimize your workload's resiliency. To minimize the chance of experiencing a full application outage, you can increase the resiliency of your solution by eliminating single points of failure and minimizing the blast radius of failures. The design approaches highlighted in this article will give you several options to strengthen the resilience of your workload and meet the reliability targets (link to RE03) that have been defined for it.

#### **Infrastructure design guidance and patterns**

At the infrastructure level, your critical flows should be supported by a redundant architecture design (link to RE:03), with resources deployed across [availability zones](https://review.learn.microsoft.com/en-us/azure/well-architected/regions-availability-zones?branch=pr-en-us-771) or regions (link to active-passive guide) with auto-scaling (link to RE:05) implemented when possible. Autoscaling will help protect your workload against unanticipated bursts in activity, further reinforcing your infrastructure.

Using a deployment stamp design pattern or a bulkhead pattern will allow you to minimize the blast radius when issues arise, helping you keep your workload available even if an individual component becomes unavailable. Use the following application design patterns in combination with your auto-scaling strategy.

-   [**Deployment stamp pattern**:](https://learn.microsoft.com/en-us/azure/architecture/patterns/deployment-stamp) The deployment stamp pattern involves provisioning, managing, and monitoring a heterogeneous group of resources to host and operate multiple workloads or tenants. Each individual copy is called a *stamp*, or sometimes a *service unit, scale unit,* or *cell.*

-   **[Bulkhead pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/bulkhead)** Partition service instances into different groups (known as pools), based on consumer load and availability requirements. This design helps to isolate failures and allows you to sustain service functionality for some consumers, even during a failure.

#### Application design guidance and patterns

As an overarching theme to your application design, avoid building monolithic applications. Using loosely coupled services and/or microservices that communicate with each other via well-defined standards, you will be able to reduce the risk of catastrophic issues when malfunctions happen to a single component and a more easily manageable workload. The design patterns listed below will help you componentize your workload and define the communications between components in a way that best meets the business requirements or your workload.

-   [**Ambassador pattern**](https://learn.microsoft.com/en-us/azure/architecture/patterns/ambassador): Separate your business logic from your networking code and resiliency logic. Create helper services that send network requests on behalf of a consumer service or application. You can use this pattern to implement retry mechanisms or circuit breaking.

-   [**Asynchronous request-reply pattern**](https://learn.microsoft.com/en-us/azure/architecture/patterns/async-request-reply): Decouple backend processing from a frontend host, where backend processing needs to be asynchronous, but the frontend still needs a clear response.

-   [**Cache-Aside pattern**](https://learn.microsoft.com/en-us/azure/architecture/patterns/cache-aside): Load data on demand into a cache from a data store. This can improve performance and also helps to maintain consistency between data held in the cache and data in the underlying data store.

-   [**Circuit Breaker pattern:**](https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker) In the context of self-preservation, circuit breakers proactively decide whether to allow an operation to proceed or return an exception based on the number of recent failures.

-   [**Claim Check pattern**](https://learn.microsoft.com/en-us/azure/architecture/patterns/claim-check): Split a large message into a claim check and a payload. Send the claim check to the messaging platform and store the payload to an external service. This pattern allows large messages to be processed, while protecting the message bus and the client from being overwhelmed or slowed down.

-   [**Competing Consumers pattern**](https://learn.microsoft.com/en-us/azure/architecture/patterns/competing-consumers): Enable multiple concurrent consumers to process messages received on the same messaging channel. With multiple concurrent consumers, a system can process multiple messages concurrently to optimize throughput, to improve scalability and availability, and to balance the workload.

-   [**Configure request timeouts**](https://learn.microsoft.com/en-us/azure/well-architected/resiliency/app-design-error-handling#configure-request-timeouts) for calls to services or databases. Database connection timeouts are typically set to 30 seconds.

-   [**Gatekeeper pattern**](https://learn.microsoft.com/en-us/azure/architecture/patterns/gatekeeper): Protect applications and services by using a dedicated host instance to broker requests between clients and the application or service. The broker validates and sanitizes the requests, and can provide an additional layer of security and limit the system\'s attack surface.

-   [**Queue-Based Load Leveling pattern**;](https://learn.microsoft.com/en-us/azure/architecture/patterns/queue-based-load-leveling) Decouple the tasks from the service in your solution by using a queue between them, so they can each run asynchronously. Using a queue that acts as a buffer between a task and a service it invokes, helps smooth intermittent heavy loads that can cause the service to fail or the task to time out. This can help to minimize the impact of peaks in demand on availability and responsiveness for both the task and the service.

-   [**Throttling pattern**:](https://learn.microsoft.com/en-us/azure/architecture/patterns/throttling) Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service. This can allow the system to continue to function and meet service level agreements, even when an increase in demand places an extreme load on resources.

### Transient fault handling and [retry pattern:](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry) 

Transient failures are normal and expected occurrences in cloud environments and having a strategy in place to handle them is an important factor in the resiliency of your workload. Typical causes of transient faults can include momentary loss of network connectivity, a dropped database connection, or a timeout when a service is busy. Refer to the transient fault handling guide (link to re06-transient-fault-handling) in this series for detailed guidance on developing a retry strategy.

**Background jobs**

Background jobs are an effective way to enhance the reliability of a system by decoupling tasks from the user interface (UI). A task should be implemented as a background job if it does not require user input or feedback and if it does not affect the UI responsiveness.

Some common examples of background jobs are: CPU-intensive jobs, such as performing complex calculations or analyzing structural models; I/O-intensive jobs, such as executing multiple storage operations or indexing large files; Batch jobs, such as updating data on a regular basis or processing tasks at a specific time; Long-running workflows, such as completing an order, or provisioning services and systems.

For more information on how to design and implement background jobs, please refer to the Background Jobs (new RE:06 background jobs article) guide.

### Self-healing guidance

Designing your workload to for self-healing means that through failure detection, automatic responses are triggered that gracefully recover critical flows and logging has been enabled to give operational insights about the nature of the failure and success of the recovery. The approaches that you take to achieve self-healing for a critical flow depend on the reliability targets (link to RE03) that have been defined for that flow and the flow's components and dependencies.

#### Infrastructure design guidance

At the infrastructure level, your critical flows should be supported by a redundant architecture design (link to RE:03) with automated failover enabled for components that support it. The following types of services can be candidates for automated failover:

-   **Compute resources:** VM Scale Sets and most PaaS compute services can be configured for automatic failover

-   **Databases:** Relational databases can be configured for automatic failover with solutions like SQL failover clusters, AlwaysOn Availability Groups, or built-in capabilities with PaaS services. NoSQL databases have similar clustering capabilities and built-in capabilities for PaaS services

-   Storage: make use of [redundant storage options](https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy) with automatic failover

#### Application design patterns

-   [**Circuit Breaker pattern**:](https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker) If a failure persists after your retry mechanism has been initiated, you risk suffering cascading failures resulting from a growing backlog of calls. A circuit breaker designed to work in conjunction with the retry mechanism will limit the risk of cascading failures by preventing the app from repeatedly trying to execute an operation that's likely to fail. 

-   [**Compensating Transaction pattern**:](https://learn.microsoft.com/en-us/azure/architecture/patterns/compensating-transaction) When you use an eventually consistent operation that consists of a series of steps, the Compensating Transaction pattern can be useful. Specifically, if one or more of the steps fail, you can use the Compensating Transaction pattern to undo the work that the steps performed. 

-   **Checkpoint long-running transactions**. Checkpoints can provide resiliency if a long-running operation fails. When the operation restarts (for example, it is picked up by another VM), it can be resumed from the last checkpoint. Consider implementing a mechanism that records state information about the task at regular intervals and save this state in durable storage that can be accessed by any instance of the process running the task. In this way, if the process is shut down, the work that it was performing can be resumed from the last checkpoint by using another instance. There are libraries that provide this functionality, such as [NServiceBus](https://docs.particular.net/nservicebus/sagas) and [MassTransit](https://masstransit-project.com/usage/sagas). They transparently persist state, where the intervals are aligned with the processing of messages from queues in Azure Service Bus. 

-   **Degrade gracefully**. Sometimes you can\'t work around a problem, but you can provide reduced functionality that is still useful. Consider an application that shows a catalog of books. If the application can\'t retrieve the thumbnail image for the cover, it might show a placeholder image. Entire subsystems might be noncritical for the application. For example, in an e-commerce site, showing product recommendations is probably less critical than processing orders. Graceful degradation can also include automatic failover operations. When a database automatically fails over to a replica due to an issue with the primary instance, performance will be degraded for a short time. 

-   [**Leader election pattern:**](https://learn.microsoft.com/en-us/azure/architecture/patterns/leader-election) When you need to coordinate a task, use Leader Election to select a coordinator. That way, the coordinator is not a single point of failure. If the coordinator fails, a new one is selected. Rather than implement a leader election algorithm from scratch, consider an off-the-shelf solution such as Zookeeper. 

-   **Block bad actors**. Just because you throttle a client, it doesn\'t mean client was acting maliciously. It just means the client exceeded their service quota. But if a client consistently exceeds their quota or otherwise behaves badly, you might block them. Define an out-of-band process for user to request getting unblocked. 

> [!NOTE]
> Ensure that you include testing of the patterns that you implement as part of your standard testing procedures.

#### Automated self-healing actions

Another approach to self-healing is the use of automated actions that are triggered by your monitoring solution when pre-determined health status changes are detected. For example, if your monitoring detects that a web app is not responding to requests, you can build automation through a Powershell script to restart the app service. Depending on your team's skill set and preferred development technologies, you can use a webhook or function can allow you to build more complex automation actions.  See the [Event based cloud automation](/azure/architecture/reference-architectures/serverless/cloud-automation) reference architecture for an example of using a function to respond to database throttling. Using automated actions can help you recover quickly and minimize the necessity of human intervention.

## Azure facilitation

Most Azure services and client SDKs include a retry mechanism. However, these differ because each service has different characteristics and requirements, and so each retry mechanism is tuned to a specific service. Refer to the service-specific guidance (link to Azure facilitation anchor in re06-transient-fault-handling article) to learn more about best practices for many Azure services.

[Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups) can be used both for notifications (like email, voice or SMS) and to trigger automated actions. You can trigger an Azure Automation runbook, Event Hubs, an Azure Function, a Logic App, or a webhook to perform an automated healing action when a failure is detected.

## Tradeoff

-   Reliability vs. cost tradeoffs. Implementing some of the self-preservation patterns, like cache aside or ambassador pattern, may increase the cost of provisioning the workload infrastructure. Consider the cost factor and only implement these approaches when there are actual benefits derived from the implementation.

-   Reliability vs. performance tradeoff. Implementing some of these patterns can result in an increase in the time it takes to complete a given transaction or application flow. Take this into account and only apply a given pattern if it is not going to affect your ability to meet your performance targets for those transactions or flows.

-   Reliability vs. user experience tradeoff. The user experience with your applications can be significantly changed due to the implementation of these patterns. For example, when using a queue-based load leveling pattern, your application may not be able to present the outcome of a request to the user real time, which may not be the right approach in all the scenarios.

#### Considerations

Each of the patterns discussed above have considerations to be familiar with. Ensure that the pattern is suitable for your workload and business requirements before implementation.

-   [Ambassador pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/ambassador#issues-and-considerations)

-   [Asynchronous Request-Reply pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/async-request-reply#issues-and-considerations)

-   [Bulkhead pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/bulkhead#issues-and-considerations)

-   [Cache-Aside pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/cache-aside#issues-and-considerations)

-   [Claim Check pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/claim-check#issues-and-considerations)

-   [Competing Consumers pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/competing-consumers#issues-and-considerations)

-   [Configure request timeouts](https://learn.microsoft.com/en-us/azure/well-architected/resiliency/app-design-error-handling#configure-request-timeouts)

-   [Gatekeeper pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/gatekeeper#issues-and-considerations)

-   [Queue-Based Load Leveling pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/queue-based-load-leveling#issues-and-considerations)

-   [Throttling pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/throttling#issues-and-considerations)

-   [Transient fault handling pattern](https://learn.microsoft.com/en-us/azure/architecture/best-practices/transient-faults#other-considerations) 

-   [Retry pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry#issues-and-considerations) 

-   [Compensating transactions pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/compensating-transaction#issues-and-considerations) 

-   [Throttling pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/throttling#issues-and-considerations) 

-   [Leader election pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/leader-election#issues-and-considerations) 

## Example

Refer to the [[reliable web app pattern for .NET]{.underline}](https://learn.microsoft.com/en-us/azure/architecture/web-apps/guides/reliable-web-app/dotnet/apply-pattern) for an example of using many of the patterns discussed in this article and a [[reference implementation]{.underline}](https://github.com/Azure/reliable-web-app-pattern-dotnet) hosted in GitHub.  

## Related links
