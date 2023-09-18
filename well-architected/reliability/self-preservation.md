---
title: Recommendations for self-healing and self-preservation
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
# Recommendations for self-healing and self-preservation

**Applies to: RE 06**

This guide describes the recommendations for building self-healing and self-preservation capabilities into your application architecture to optimize reliability.

Self-preservation capabilities add resilience to your workload. They reduce the likelihood of a full outage and allow your workload to operate in a degraded state while failed components are recovered. Self-healing capabilities help you avoid downtime by building in failure detection and automatic corrective actions to respond to different failure types.

This guide describes design patterns that focus on self-preservation and self-healing. Incorporate them into your workload to strengthen its resiliency and recoverability. If you don't implement patterns, your apps are at risk of failure when inevitable problems arise.

**Definitions**

| Term | Definition |
|------|------------|
| Self-healing | The ability of your workload to automatically recover affected components and fail over to redundant infrastructure. |
| Self-preservation | The ability of your workload to be resilient against potential problems. |

## Key design strategies

### Self-preservation guidance

To design your workload for self-preservation, follow infrastructure and application architecture design patterns to optimize your workload's resiliency. To minimize the chance of experiencing a full application outage, increase the resiliency of your solution by eliminating single points of failure and minimizing the blast radius of failures. The design approaches in this article provide several options to strengthen the resilience of your workload and meet your workload's defined [reliability targets](business-metrics.md).

#### Infrastructure design guidance and patterns

At the infrastructure level, a [redundant architecture design](business-metrics.md) should support your critical flows, with resources deployed across [availability zones](regions-availability-zones.md) or [regions](highly-available-multi-region-design.md). Implement [autoscaling](scaling.md) when possible. Autoscaling helps protect your workload against unanticipated bursts in activity, further reinforcing your infrastructure.

Use the Deployment Stamps pattern or the Bulkhead pattern to minimize the blast radius when problems arise. These patterns help to keep your workload available if an individual component is unavailable. Use the following application design patterns in combination with your autoscaling strategy.

- [**Deployment Stamps pattern**](/azure/architecture/patterns/deployment-stamp): Provision, manage, and monitor a varied group of resources to host and operate multiple workloads or tenants. Each individual copy is called a *stamp*, or sometimes a *service unit*, *scale unit*, or *cell*.

- [**Bulkhead pattern**](/azure/architecture/patterns/bulkhead): Partition service instances into different groups, known as *pools*, based on the consumer load and availability requirements. This design helps to isolate failures and allows you to sustain service functionality for some consumers, even during a failure.

#### Application design guidance and patterns

Avoid building monolithic applications in your application design. Use loosely coupled services or microservices that communicate with each other via well-defined standards to reduce the risk of extensive problems when malfunctions happen to a single component. Implement this practice to create a more manageable workload. The following design patterns help you organize your workload and define the communications between components in a way that best meets your business requirements.

- [**Ambassador pattern**](/azure/architecture/patterns/ambassador): Separate your business logic from your networking code and resiliency logic. Create helper services that send network requests on behalf of a consumer service or application. You can use this pattern to implement retry mechanisms or circuit breaking.

- [**Asynchronous Request-Reply pattern**](/azure/architecture/patterns/async-request-reply): Decouple back-end processing from a front-end host if back-end processing needs to be asynchronous, but the front end needs a clear response.

- [**Cache-Aside pattern**](/azure/architecture/patterns/cache-aside): Load data on demand from a data store into a cache. This pattern can improve performance and help maintain consistency between data that's held in the cache and data that's in the underlying data store.

- [**Circuit Breaker pattern**](/azure/architecture/patterns/circuit-breaker): Use circuit breakers to proactively determine whether to allow an operation to proceed or to return an exception based on the number of recent failures.

- [**Claim Check pattern**](/azure/architecture/patterns/claim-check): Split a large message into a claim check and a payload. Send the claim check to the messaging platform and store the payload in an external service. This pattern allows large messages to be processed while protecting the message bus and keeping the client from being overwhelmed or slowed down.

- [**Competing Consumers pattern**](/azure/architecture/patterns/competing-consumers): Enable multiple concurrent consumers to process messages that are received on the same messaging channel. A system can process multiple messages concurrently, which optimizes throughput, improves scalability and availability, and balances the workload.

- [**Configure request timeouts**](/azure/well-architected/resiliency/app-design-error-handling#configure-request-timeouts): Configure request timeouts for calls to services or databases. Database connection timeouts are typically set to 30 seconds.

- [**Gatekeeper pattern**](/azure/architecture/patterns/gatekeeper): Protect applications and services by using a dedicated host instance to broker requests between clients and the application or service. The broker validates and sanitizes the requests and can provide an extra layer of security to limit the system's attack surface.

- [**Queue-Based Load Leveling pattern**](/azure/architecture/patterns/queue-based-load-leveling): Decouple the tasks from the service in your solution by using a queue between them so they can each run asynchronously. Use a queue as a buffer between a task and a service it invokes to help smooth intermittent heavy loads that can cause the service to fail or the task to time out. This pattern can help minimize the effect of peaks in demand on availability and responsiveness for the task and the service.

- [**Throttling pattern**](/azure/architecture/patterns/throttling): Control the consumption of resources that are used by an instance of an application, an individual tenant, or an entire service. This pattern allows the system to continue to function and meet service-level agreements (SLAs), even when an increase in demand places an extreme load on resources.

- **[Transient Fault Handling pattern and Retry pattern](/azure/architecture/patterns/retry)**: Implement a strategy to handle transient failures to provide resiliency in your workload. Transient failures are normal and expected occurrences in cloud environments. Typical causes of transient faults include momentary loss-of-network connectivity, a dropped database connection, or a timeout when a service is busy. For more information about developing a retry strategy, see [the transient fault handling guide](handle-transient-faults.md) in this series.

#### Background jobs

Background jobs are an effective way to enhance the reliability of a system by decoupling tasks from the user interface (UI). Implement a task as a background job if it doesn't require user input or feedback and if it doesn't affect UI responsiveness.

Common examples of background jobs are:

- CPU-intensive jobs, such as performing complex calculations or analyzing structural models.
- I/O-intensive jobs, such as running multiple storage operations or indexing large files.
- Batch jobs, such as updating data regularly or processing tasks at a specific time.
- Long-running workflows, such as completing an order or provisioning services and systems.

For more information, see [Recommendations for background jobs](background-jobs.md)<!--RE:06 check link after file is named-->.

### Self-healing guidance

To design your workload for self-healing, implement failure detection so automatic responses are triggered and critical flows gracefully recover. Enable logging to provide operational insights about the nature of the failure and the success of the recovery. The approaches that you take to achieve self-healing for a critical flow depend on the [reliability targets](business-metrics.md) that are defined for that flow and the flow's components and dependencies.

#### Infrastructure design guidance

At the infrastructure level, your critical flows should be supported by a [redundant architecture design](business-metrics.md) with automated failover enabled for components that support it. You can enable automated failover for the following types of services:

- **Compute resources**: Azure Virtual Machine Scale Sets and most platform as a service (PaaS) compute services can be configured for automatic failover.

- **Databases**: Relational databases can be configured for automatic failover with solutions like Azure SQL failover clusters, Always On availability groups, or built-in capabilities with PaaS services. NoSQL databases have similar clustering capabilities and built-in capabilities for PaaS services.

- **Storage**: Use [redundant storage options](/azure/storage/common/storage-redundancy) with automatic failover.

#### Application design guidance and patterns

- **Block bad actors**: If you throttle a client, it doesn't mean that client was acting maliciously. It might mean that the client exceeded their service quota. But if a client consistently exceeds their quota or otherwise behaves poorly, you might block them. Define an out-of-band process for a client to request getting unblocked.

- [**Circuit Breaker pattern**](/azure/architecture/patterns/circuit-breaker): If a failure persists after your retry mechanism is initiated, you risk cascading failures resulting from a growing backlog of calls. A circuit breaker that's designed to work with the retry mechanism limits the risk of cascading failures by preventing the app from repeatedly trying to run an operation that's likely to fail.

- [**Compensating Transaction pattern**](/azure/architecture/patterns/compensating-transaction): If you use an eventually consistent operation that consists of a series of steps, implement the Compensating Transaction pattern. If one or more of the steps fail, you can use this pattern to undo the work that the steps performed.

- **Degrade gracefully**: Sometimes you can't work around a problem, but you can provide reduced functionality. Consider an application that shows a catalog of books. If the application can't retrieve the thumbnail image for the cover, it might show a placeholder image. Entire subsystems might be noncritical for the application. For example, for an e-commerce website, showing product recommendations is probably less critical than processing orders. Graceful degradation can also include automatic failover operations. When a database automatically fails over to a replica due to a problem with the primary instance, performance is degraded for a short time.

- [**Leader Election pattern**](/azure/architecture/patterns/leader-election): When you need to coordinate a task, use leader election to select a coordinator so one coordinator isn't a single point of failure. If the coordinator fails, a new one is selected. Rather than implement a leader election algorithm from scratch, consider an off-the-shelf solution, such as [ZooKeeper](https://zookeeper.apache.org/doc/current/recipes.html#sc_leaderElection).

- **Test patterns**: Include testing of the patterns that you implement as part of your standard testing procedures.

- **Use checkpoints for long-running transactions**: Checkpoints can provide resiliency if a long-running operation fails. When the operation restarts, for example if it's picked up by another virtual machine, it can resume from the last checkpoint. Consider implementing a mechanism that records state information about the task at regular intervals. Save this state in durable storage that can be accessed by any instance of the process running the task. If the process is shut down, the work that it was performing can be resumed from the last checkpoint by using another instance. There are libraries that provide this functionality, such as [NServiceBus](https://docs.particular.net/nservicebus/sagas) and [MassTransit](https://masstransit-project.com/usage/sagas). They transparently persist state, where the intervals are aligned with the processing of messages from queues in Azure Service Bus.

#### Automated self-healing actions

Another approach to self-healing is the use of automated actions that are triggered by your monitoring solution when pre-determined health status changes are detected. For example, if your monitoring detects that a web app isn't responding to requests, you can build automation through a Powershell script to restart the app service. Depending on your team's skill set and preferred development technologies, use a webhook or function to build more complex automation actions. See the [Event-based cloud automation](/azure/architecture/reference-architectures/serverless/cloud-automation) reference architecture for an example of using a function to respond to database throttling. Using automated actions can help you recover quickly and minimize the necessity of human intervention.

## Azure facilitation

Most Azure services and client SDKs include a retry mechanism. But they differ because each service has different characteristics and requirements, so each retry mechanism is tuned to a specific service. For more information, see [Recommendations for transient fault handling](handle-transient-faults.md).

Use [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups) for notifications, like email, voice or SMS, and to trigger automated actions. When you're notified of a failure, trigger an Azure Automation runbook, Azure Event Hubs, an Azure function, a logic app, or a webhook to perform an automated healing action.

## Tradeoffs 

- *Reliability vs. cost tradeoff.* When you implement some of the self-preservation patterns, like the Cache-Aside pattern or the Ambassador pattern, the cost of provisioning the workload infrastructure might increase. Implement these approaches only when the implementation provides benefits.

- *Reliability vs. performance tradeoff.* When you implement some of these patterns, the time to complete a given transaction or application flow might increase. Apply a pattern only if it doesn't affect your ability to meet your performance targets for those transactions or flows.

- *Reliability vs. user experience tradeoff.* When you implement some of these patterns, the user's experience with your applications might change significantly. For example, when using a Queue-Based Load Leveling pattern, your application might not present a real-time outcome of a request to the user, which might not be the right approach in all scenarios.

### Considerations

Familiarize yourself with the considerations for each pattern. Ensure that the pattern is suitable for your workload and business requirements before implementation.

- [Ambassador pattern](/azure/architecture/patterns/ambassador#issues-and-considerations)
- [Asynchronous Request-Reply pattern](/azure/architecture/patterns/async-request-reply#issues-and-considerations)
- [Bulkhead pattern](/azure/architecture/patterns/bulkhead#issues-and-considerations)
- [Cache-Aside pattern](/azure/architecture/patterns/cache-aside#issues-and-considerations)
- [Claim Check pattern](/azure/architecture/patterns/claim-check#issues-and-considerations)
- [Compensating Transaction pattern](/azure/architecture/patterns/compensating-transaction#issues-and-considerations)
- [Competing Consumers pattern](/azure/architecture/patterns/competing-consumers#issues-and-considerations)
- [Configure request timeouts](/azure/well-architected/resiliency/app-design-error-handling#configure-request-timeouts)
- [Gatekeeper pattern](/azure/architecture/patterns/gatekeeper#issues-and-considerations)
- [Leader Election pattern](/azure/architecture/patterns/leader-election#issues-and-considerations)
- [Queue-Based Load Leveling pattern](/azure/architecture/patterns/queue-based-load-leveling#issues-and-considerations)
- [Retry pattern](/azure/architecture/patterns/retry#issues-and-considerations)
- [Throttling pattern](/azure/architecture/patterns/throttling#issues-and-considerations)
- [Transient Fault Handling pattern](/azure/architecture/best-practices/transient-faults#other-considerations)

## Example

For example use cases of some patterns, see the [reliable web app pattern for .NET](/azure/architecture/web-apps/guides/reliable-web-app/dotnet/apply-pattern). Follow [these steps to deploy a reference implementation](https://github.com/Azure/reliable-web-app-pattern-dotnet).

## Related links

- [Reliability patterns](reliability-patterns.md)
- [Cloud design patterns](/azure/architecture/patterns)
- [Design for self-healing](/azure/architecture/guide/design-principles/self-healing)
