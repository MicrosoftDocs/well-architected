---
title: Recommendations for self-healing and self-preservation
description: Learn how to build self-healing and self-preservation capabilities into your application architecture to optimize reliability.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for self-healing and self-preservation

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:07**| Strengthen the resiliency of your workload by implementing self-preservation and self-healing measures. Use built-in features and well-established cloud patterns to help your workload remain functional during and recover from incidents.|
|---|---|

//suggestion: remove. not sure why this dependency is different than in other cases
**Related guides:** [Background jobs](../design-guides/background-jobs.md) | [Transient faults](../design-guides/handle-transient-faults.md)
//end suggestion

This guide describes the recommendations for building self-preservation and self-healing capabilities into your application architecture to optimize reliability.

Self-preservation capabilities add resilience to your workload. They reduce the likelihood of a full outage and allow your workload to operate normally, or in a degraded state, when failures occur. Self-healing capabilities help you avoid downtime by building in failure detection and automatic corrective actions to respond to failures.

//suggestion: remove. duplicates the first paragraph, and also the article is not about design patterns alone anymore
This guide describes design patterns that focus on self-preservation and self-healing. Incorporate them into your workload to strengthen its resiliency and recoverability. If you don't implement patterns, your apps are at risk of failure when inevitable problems arise.
//end suggestion

**Definitions**

| Term | Definition |
|------|------------|
| Self-healing | The ability of your workload to automatically resolve issues by recovering affected components and if needed, failing over to redundant infrastructure. |
| Self-preservation | The ability of your workload to be resilient against potential problems. |

## Key design strategies

### Design for redundancy

One of the most effective strategies to protect your workload from malfunctions is to build redundancy into all of its components and avoid single points of failure. Being able to fail components or the entire workload over to redundant resources provides an efficient way to handle most faults in your system. 

Build redundancy at different levels, consider redundant infrastructure components such as compute, network, and storage; and consider deploying multiple instances of your solution. Depending on your business requirements, you can build redudancy within a single region or across regions. You can also decide whether you need an active-active or an active-passive design to meet your recovery requirements. See the [redundancy](./redundancy.md), [regions and availability zones](./regions-availability-zones.md), and [highly-available multi-region design](./highly-available-multi-region-design.md) Reliability articles for in-depth guidance on this strategy.

### Design for handling transient faults

[Transient faults](../design-guides/handle-transient-faults.md), like network timeouts, are a common issue for cloud workloads, so having mechanisms in place to handle them can minimize downtime and troubleshooting efforts as you operate your workload in production. Since most operations that fail due to a transient faults will succeed if sufficient time is allowed before retrying the operation, using a retry mechansim is the most common approach for dealing with transient faults. When designing your retry strategy, consider the following:

- **Built-in retry functionality:** Many cloud services, especially platform as a service (PaaS) and software as a service (SaaS) offerings have built-in retry functionality. Review the retry options available for your components and determine whether you'll need to build any custom functionality to meet your requirements.
- **Determine an appropriate retry count and interval:** Finding the right retry count and interval for a given component can be a matter of trial and error. If you don't wait long enough to retry or you don't retry often enough, the faulting operation may never succeed. On the other hand, waiting too long between tries or trying too often might cause resource constraints by overloading threads, connections, or memory. 
- **Log and track transient and non-transient faults:** Augmenting your fault handling mechanisms with historical data can help you identify components that can be further optimized. If you see a pattern of many retries for a given component, it could be a sign that the component has some inefficiency or that an issue has developed in your workload that needs to be investigated. 
- **Manage operations that continually fail:** When operations continually fail, a retry mechasims might not be sufficient to keep your workload operational. You might need to fail the corresponding component over to another instance or implement a [circuit breaker](/azure/architecture/patterns/circuit-breaker) to prevent continually retrying a failing operation.

Refer to the [Transient faults](../design-guides/handle-transient-faults.md) design guide for detailed guidance on this strategy and more considerations.

### Implement background jobs

Background jobs are an effective way to enhance the reliability of a system by decoupling tasks from the user interface (UI). Implement a task as a background job if it doesn't require user input or feedback and if it doesn't affect UI responsiveness.

Common examples of background jobs are:

- CPU-intensive jobs, such as performing complex calculations or analyzing structural models.
- I/O-intensive jobs, such as running multiple storage operations or indexing large files.
- Batch jobs, such as updating data regularly or processing tasks at a specific time.
- Long-running workflows, such as completing an order or provisioning services and systems.

When developing background jobs for your workload, consider the following:

- **Choose the right triggers:** Determine whether an event-driven or schedule-driven trigger is appropriate for your use case. 
- **Partition background jobs:** Determine whether background jobs should be colocated with other tasks on the same compute resources.
- **Orchestrate multiple tasks:** Determine whether a given background job is complex enough to divide into smaller discrete steps or subtasks.
- **Make jobs resilient:** Like other components, background jobs should be optimized for reliability. Using checkpoints and queues can help and resiliency.
- **Account for messaging inconsistencies:**  For tasks that are initiated by messages or that process messages, design a strategy to handle inconsistencies like messages that arrive out of order or that are delivered repeatedly, and for messages that repeatedly cause errors.
- **Make jobs scalable:** Ensure that your scaling stratgey includes the components for background jobs. Like other components, they will need to scale when demands change.

Refer to the [background jobs](../design-guides/background-jobs.md) design guide for detailed guidance on this strategy and more considerations.

### Design for self-preservation

To design your workload for self-preservation, follow infrastructure and application architecture design patterns to optimize your workload's resiliency. To minimize the chance of experiencing a full application outage, increase the resiliency of your solution by eliminating single points of failure and minimizing the blast radius of failures. The design approaches in this article provide several options to strengthen the resilience of your workload and meet your workload's defined [reliability targets](metrics.md).

#### Infrastructure design guidance and patterns

At the infrastructure level, a [redundant architecture design](metrics.md) should support your critical flows, with resources deployed across [availability zones](regions-availability-zones.md) or [regions](highly-available-multi-region-design.md). Implement [autoscaling](scaling.md) when possible. Autoscaling helps protect your workload against unanticipated bursts in activity, further reinforcing your infrastructure.

Use the Deployment Stamps pattern or the Bulkhead pattern to minimize the blast radius when problems arise. These patterns help to keep your workload available if an individual component is unavailable. Use the following application design patterns in combination with your autoscaling strategy.

- [**Deployment Stamps pattern**](/azure/architecture/patterns/deployment-stamp): Provision, manage, and monitor a varied group of resources to host and operate multiple workloads or tenants. Each individual copy is called a *stamp*, or sometimes a *service unit*, *scale unit*, or *cell*.

- [**Bulkhead pattern**](/azure/architecture/patterns/bulkhead): Partition service instances into different groups, known as *pools*, based on the consumer load and availability requirements. This design helps to isolate failures and allows you to sustain service functionality for some consumers, even during a failure.

#### Application design guidance and patterns

Avoid building monolithic applications in your application design. Use loosely coupled services or microservices that communicate with each other via well-defined standards to reduce the risk of extensive problems when malfunctions happen to a single component. For example, you may standardize the use of a service bus to handle all asynchronous communication. Standardizing communication protocols ensures that applications design is consistent and simplified, which makes the workload more reliable and easier to troubleshoot when malfunctions happen. When practical, prefer asynchronous communication between components over synchronous communication to minimize timeout issues, like dead-lettering. 

Use industry-proven patterns to help you develop your design standards and simplify aspects of the architecture. Design patterns that can help support reliability can be found in the [Reliability patterns](./design-patterns.md) article.

### Design for self-healing

To design your workload for self-healing, implement failure detection so automatic responses are triggered and critical flows gracefully recover. Enable logging to provide operational insights about the nature of the failure and the success of the recovery. The approaches that you take to achieve self-healing for a critical flow depend on the [reliability targets](metrics.md) that are defined for that flow and the flow's components and dependencies.

#### Infrastructure design guidance

At the infrastructure level, your critical flows should be supported by a redundant architecture design, with automated failover enabled for components that support it. You can enable automated failover for the following types of services:

- **Compute resources**: Azure Virtual Machine Scale Sets and most platform as a service (PaaS) compute services can be configured for automatic failover.

- **Databases**: Relational databases can be configured for automatic failover with solutions like Azure SQL failover clusters, Always On availability groups, or built-in capabilities with PaaS services. NoSQL databases have similar clustering capabilities and built-in capabilities for PaaS services.

- **Storage**: Use [redundant storage options](/azure/storage/common/storage-redundancy) with automatic failover.

#### Application design guidance

In addition to using [design patterns](./design-patterns.md) that support reliability, other strategies that can help you develop self-healing mechanisms include:

- **Use checkpoints for long-running transactions**: Checkpoints can provide resiliency if a long-running operation fails. When the operation restarts, for example if it's picked up by another virtual machine, it can resume from the last checkpoint. Consider implementing a mechanism that records state information about the task at regular intervals. Save this state in durable storage that can be accessed by any instance of the process running the task. If the process is shut down, the work that it was performing can be resumed from the last checkpoint by using another instance. There are libraries that provide this functionality, such as [NServiceBus](https://docs.particular.net/nservicebus/sagas) and [MassTransit](https://masstransit-project.com/usage/sagas). They transparently persist state, where the intervals are aligned with the processing of messages from queues in Azure Service Bus.

- **Implement automated self-healing actions:** Use automated actions that are triggered by your monitoring solution when pre-determined health status changes are detected. For example, if your monitoring detects that a web app isn't responding to requests, you can build automation through a PowerShell script to restart the app service. Depending on your team's skill set and preferred development technologies, use a webhook or function to build more complex automation actions. See the [Event-based cloud automation](/azure/architecture/reference-architectures/serverless/cloud-automation) reference architecture for an example of using a function to respond to database throttling. Using automated actions can help you recover quickly and minimize the necessity of human intervention.

## Implement a graceful degradation mode

Implementing graceful degradation in your workload helps it continue to operate with reduced functionality when some of its components fail. Enabling business continuity during failure states.

//**TODO:** some points to expand here
- buid mechanisms to detect when a failure is affecting functionality and automatically switch to the graceful degratation mode
- Build a graceful Degradation of User Experience: Informing users about the issue and restricting access to broken areas of the solution. 
- Build alternatives paths to complete the essential functionality to maintain business continuity. For example, if a database is down, the application might switch to a read-only mode using cached data. For example, if a payment gateway is down, allow users to save their cart and complete the purchase later.
//***end TODO:***

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