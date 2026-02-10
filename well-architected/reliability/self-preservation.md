---
title: Architecture strategies for self-healing and self-preservation
description: Learn how to build self-healing and self-preservation capabilities into your application architecture to optimize reliability.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: concept-article
---

# Architecture strategies for self-healing and self-preservation

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:07**| Strengthen the resiliency of your workload by implementing self-preservation and self-healing measures. Use built-in features and well-established cloud patterns to help your workload remain functional during and recover from incidents.|
|---|---|

This guide describes the recommendations for building self-preservation and self-healing capabilities into your application architecture to optimize reliability.

Self-preservation capabilities add resilience to your workload. They reduce the likelihood of a full outage and allow your workload to operate normally, or in a degraded state, when failures occur. Self-healing capabilities help you avoid downtime by building in failure detection and automatic corrective actions to respond to failures.

**Definitions**

| Term | Definition |
|------|------------|
| Self-healing | The ability of your workload to automatically resolve issues by recovering affected components and if needed, failing over to redundant infrastructure. |
| Self-preservation | The ability of your workload to be resilient against potential problems. |


## Design for redundancy

One of the most effective strategies to protect your workload from malfunctions is to build redundancy into all of its components and avoid single points of failure. Being able to fail components or the entire workload over to redundant resources provides an efficient way to handle most faults in your system.

Build redundancy at different levels, consider redundant infrastructure components such as compute, network, and storage, and consider deploying multiple instances of your solution. Depending on your business requirements, you can build redundancy within a single region or across regions. You can also decide whether you need an active-active or an active-passive design to meet your recovery requirements. For more information, see [Architecture strategies for designing for redundancy](./redundancy.md) and [Architecture strategies for using availability zones and regions](../design-guides/regions-availability-zones.md).

## Design for self-preservation

To design your workload for self-preservation, follow infrastructure and application architecture design patterns to optimize your workload's resiliency. To minimize the chance of experiencing a full application outage, increase the resiliency of your solution by eliminating single points of failure and minimizing the blast radius of failures. The design approaches in this article provide several options to strengthen the resilience of your workload and meet your workload's defined [reliability targets](metrics.md).

### Infrastructure design guidance and patterns

At the infrastructure level, a [redundant architecture design](redundancy.md) should support your critical flows, with resources deployed across [availability zones or regions](../design-guides/regions-availability-zones.md). Implement [autoscaling](scaling.md) when possible. Autoscaling helps protect your workload against unanticipated bursts in activity, further reinforcing your infrastructure.

Use the Deployment Stamps pattern or the Bulkhead pattern to minimize the blast radius when problems arise. These patterns help to keep your workload available if an individual component is unavailable. Use the following application design patterns in combination with your autoscaling strategy.

- [**Deployment Stamps pattern**](/azure/architecture/patterns/deployment-stamp): Provision, manage, and monitor a varied group of resources to host and operate multiple workloads or tenants. Each individual copy is called a *stamp*, or sometimes a *service unit*, *scale unit*, or *cell*.

- [**Bulkhead pattern**](/azure/architecture/patterns/bulkhead): Partition service instances into different groups, known as *pools*, based on the consumer load and availability requirements. This design helps to isolate failures and allows you to sustain service functionality for some consumers, even during a failure.

### Application design guidance and patterns

Avoid building monolithic applications in your application design. Use loosely coupled services or microservices that communicate with each other via well-defined standards to reduce the risk of extensive problems when malfunctions happen to a single component. For example, you may standardize the use of a service bus to handle all asynchronous communication. Standardizing communication protocols ensures that applications design is consistent and simplified, which makes the workload more reliable and easier to troubleshoot when malfunctions happen. When practical, prefer asynchronous communication between components over synchronous communication to minimize timeout issues.

Use industry-proven patterns to help you develop your design standards and simplify aspects of the architecture. Design patterns that can help support reliability can be found in the [Reliability patterns](./design-patterns.md) article.

## Design for self-healing

To design your workload for self-healing, implement failure detection so automatic responses are triggered and critical flows gracefully recover. Enable logging to provide operational insights about the nature of the failure and the success of the recovery. The approaches that you take to achieve self-healing for a critical flow depend on the [reliability targets](metrics.md) that are defined for that flow and the flow's components and dependencies.

### Infrastructure design guidance

At the infrastructure level, your critical flows should be supported by a redundant architecture design, with automated failover enabled for components that support it. You can enable automated failover for the following types of services:

- **Compute resources**: Azure Virtual Machine Scale Sets and most platform as a service (PaaS) compute services can be configured for automatic failover.

- **Databases**: Relational databases can be configured for automatic failover with solutions like Azure SQL failover clusters, Always On availability groups, or built-in capabilities with PaaS services. NoSQL databases have similar clustering capabilities and built-in capabilities for PaaS services.

- **Storage**: Use [redundant storage options](/azure/storage/common/storage-redundancy) with automatic failover.

### Application design guidance

In addition to using [design patterns](./design-patterns.md) that support reliability, other strategies that can help you develop self-healing mechanisms include:

- **Use checkpoints for long-running transactions**: Checkpoints can provide resiliency if a long-running operation fails. When the operation restarts, for example if it's picked up by another virtual machine, it can resume from the last checkpoint. Consider implementing a mechanism that records state information about the task at regular intervals. Save this state in durable storage that can be accessed by any instance of the process running the task. If the process is shut down, the work that it was performing can be resumed from the last checkpoint by using another instance. There are libraries that provide this functionality, such as [NServiceBus](https://docs.particular.net/nservicebus/sagas) and [MassTransit](https://masstransit-project.com/usage/sagas). They transparently persist state, where the intervals are aligned with the processing of messages from queues in Azure Service Bus.

- **Implement automated self-healing actions:** Use automated actions that are triggered by your monitoring solution when pre-determined health status changes are detected.

  For example, if your monitoring detects that a web app isn't responding to requests, you can build automation through a PowerShell script to restart the app service. Depending on your team's skill set and preferred development technologies, use a webhook or function to build more complex automation actions. See the [Event-based cloud automation](/azure/architecture/reference-architectures/serverless/cloud-automation) reference architecture for an example of using a function to respond to database throttling. Using automated actions can help you recover quickly and minimize the necessity of human intervention.

  Use self-healing patterns and features specific to the technology. For example, instead of a corrupt message in a queue being repeatedly processed, potentially blocking future message processing, design remediation approaches such as using a [*dead-letter queue*](/azure/service-bus-messaging/service-bus-dead-letter-queues). You automate moving problematic messages to the queue, but handling the items is typically a manual evaluation followed by a scenario-specific remediation step.

## Implement a graceful degradation mode

Despite your self-preservation and self-healing mechanisms, you may still encounter situations where one or more components malfunction to the extent that they become unavailable for some amount of time. In these cases, ideally, your workload can maintain enough functionality for business to continue in a degraded state. To ensure that this is possible, design and implement a graceful degradation mode. This is a distinct workflow that is enabled in reaction to failed components. Considerations for the design and implementation include:

- **Failure detection and automated initiation:** Your monitoring and alerting systems should detect degraded and failed components, so use those signals to build a workflow that determines when switching to your graceful degradation mode is necessary. The workflow should then automatically reroute calls to and from affected components to alternative components, or other similar actions.
- **Implement a degraded user experience:** Include a notification mechanism for users in your graceful degradation mode to ensure that they know what functionality remains and what has changed. This typically is reflected in messages tied to different functions of the workload, like a pop-up when adding items to a cart, for example.
- **Build alternatives paths to complete your workload's essential functions:** Reflect on your workload's [critical flows](../design-guides/optimize-workload-using-flows.md) and determine how you can maintain those flows when core components are unavailable. For example, if a database is down, the application might switch to a read-only mode using cached data. To further illustrate this example, if a payment gateway is down, using cached data might allow users to save their cart and complete the purchase later.

## Implement mechanisms for handling transient faults

[Transient faults](../design-guides/handle-transient-faults.md), like network timeouts, are a common issue for cloud workloads, so having mechanisms in place to handle them can minimize downtime and troubleshooting efforts as you operate your workload in production. Since most operations that fail due to a transient fault will succeed if sufficient time is allowed before retrying the operation, using a retry mechanism is the most common approach for dealing with transient faults. When designing your retry strategy, consider the following:

Refer to the [Transient faults](../design-guides/handle-transient-faults.md) design guide for a full review of recommendations and considerations.

## Implement background jobs

Background jobs are an effective way to enhance the reliability of a system by decoupling tasks from the user interface (UI). Implement a task as a background job if it doesn't require user input or feedback and if it doesn't affect UI responsiveness.

Common examples of background jobs are:

- CPU-intensive jobs, such as performing complex calculations or analyzing structural models.
- I/O-intensive jobs, such as running multiple storage operations or indexing large files.
- Batch jobs, such as updating data regularly or processing tasks at a specific time.
- Long-running workflows, such as completing an order or provisioning services and systems.

Refer to the [background jobs](../design-guides/background-jobs.md) design guide for detailed guidance for a full review of recommendations and considerations.

## Azure facilitation

Most Azure services and client SDKs include a retry mechanism. But they differ because each service has different characteristics and requirements, so each retry mechanism is tuned to a specific service. For more information, see [Recommendations for transient fault handling](handle-transient-faults.md).

Use [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups) for notifications, like email, voice or SMS, and to trigger automated actions. When you're notified of a failure, trigger an Azure Automation runbook, Azure Event Hubs, an Azure function, a logic app, or a webhook to perform an automated healing action.

## Example

For example use cases of some patterns, see the [reliable web app pattern for .NET](/azure/architecture/web-apps/guides/reliable-web-app/dotnet/apply-pattern). Follow [these steps to deploy a reference implementation](https://github.com/Azure/reliable-web-app-pattern-dotnet).

## Related links

- [Reliability patterns](design-patterns.md)
- [Handle transient faults](../design-guides/handle-transient-faults.md)
- [Develop background jobs](../design-guides/background-jobs.md)
- [Cloud design patterns](/azure/architecture/patterns)
- [Design for self-healing](/azure/architecture/guide/design-principles/self-healing)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 
