---
title: Design a reliable scaling strategy 
description: Learn about recommendations for designing a reliable scaling strategy, including Azure facilitation and tradeoff considerations.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Design a reliable scaling strategy

**Applies to this Well-Architected Framework Reliability checklist recommendation:**

|[RE:05](checklist.md)|Implement a timely and reliable scaling strategy at the application, data, and infrastructure levels.  |
|---|---|

**Related guide:** [Data partitioning](partition-data.md)

This guide describes the recommendations for designing a reliable scaling strategy.

**Definitions**

| Term | Definition |
| --------- | ---------------- |
| Vertical scaling | A scaling approach that adds compute capacity to existing resources. (Scaling from a D4s_v3 to a D8s_v3, for example.) |
| Horizontal scaling | A scaling approach that adds instances of a given type of resource. (Expanding a web pool from two to four VMs, for example.) |
| Autoscaling | A scaling approach that lets you automatically scale horizontally when a set of conditions are met. Many Azure products support autoscaling.  |

> [!NOTE]
> Scaling operations can be static (regularly scheduled daily scaling to accommodate normal load patterns), automatic (an automated process in response to conditions being met), or manual (an operator performs a one-time scaling operation in reaction to an unanticipated load change). Both vertical and horizontal scaling can be performed via any of these methods. However, automatic vertical scaling requires additional custom automation development and can cause downtime depending on the resources being scaled.

## Key design strategies

To design a reliable scaling strategy for your workloads, focus on identifying load patterns for the user and system flows for each workload that leads to a scaling operation. Here are examples of the different load patterns:

- **Static**: Every night by 11 PM EST, the number of active users is below 100 and the CPU utilization for the app servers drops by 90% across all nodes.
- **Dynamic, regular, and predictable**: Every Monday morning, 1000 employees across multiple regions sign in to the ERP system.
- **Dynamic, irregular, and predictable**: A product launch happens on the first day of the month and there's historical data from previous launches on how the traffic increases in these situations.
- **Dynamic, irregular, and unpredictable**: A large scale event causes a spike in demand for a product. For example, companies manufacturing and selling dehumidifiers can experience a sudden surge in traffic after a hurricane or other flooding event when people in affected areas need to dry rooms in their home.

After you've identified these types of load patterns, you can:

- Identify how the load change associated with each pattern affects your infrastructure.
- Build automation to address the scaling reliably.

For the previous examples, your scaling strategies could be:

- **Static**: You have a scheduled scale of your compute nodes to the minimum count (2) between 11 PM and 6 AM EST.
- **Dynamic, regular, and predictable**: You have a scheduled scale out of your compute nodes to the normal daily capacity before the first region starts work.
- **Dynamic, irregular, and predictable**: You define a one-time scheduled scale up of your compute and database instances on the morning of a product launch, and you scale back down after one week.
- **Dynamic, irregular, and unpredictable**: You have autoscale thresholds defined to account for unplanned traffic spikes.

When designing your scaling automation, be sure to account for these issues:

- **That all components of your workload should be candidates for scaling implementation**. In most cases, global services like [Microsoft Entra ID](/azure/active-directory/fundamentals/whatis) scale automatically and transparently to you and your customers. Be sure to understand the scaling capabilities of your networking ingress and egress controllers and your load balancing solution.
- **Those components that can't be scaled out**. An example would be large, relational databases that don't have sharding enabled and can't be refactored without significant impact. Document the resource limits published by your cloud provider and monitor those resources closely. Include those specific resources in your future planning for migrating to scalable services.
- **The time it takes to perform the scaling operation so that you properly schedule the operation to happen before the extra load hits your infrastructure**. For example, if a component like API Management takes 45 minutes to scale, adjusting the scaling threshold to 65% instead of 90% might help you scale earlier and prepare for the anticipated increase in load.
- **The relationship of the flow’s components in terms of order of scale operations**. Ensure that you don’t inadvertently overload a downstream component by scaling an upstream component first.
- **Any stateful application elements that might be interrupted by a scaling operation and any session affinity (or session stickiness) that's implemented**. Stickiness can limit your scaling ability and introduces single points of failure.
- **Potential bottlenecks**. Scaling out doesn't fix every performance issue. For example, if your backend database is the bottleneck, it doesn't help to add more web servers. Identify and resolve the bottlenecks in the system first before just adding more instances. Stateful parts of the system are the most likely cause of bottlenecks.

Following the [deployment stamp](/azure/architecture/patterns/deployment-stamp) design pattern helps with your overall infrastructure management. Basing your scaling design on stamps as units of scale is also beneficial. And it helps you tightly control your scaling operations across multiple workloads and subsets of workloads. Rather than managing the scaling schedules and autoscaling thresholds of many distinct resources, you can apply a limited set of scaling definitions to a deployment stamp and then mirror that across stamps as needed.

## Azure facilitation

An autoscaling feature is available in [many Azure services](/azure/azure-monitor/autoscale/autoscale-overview#supported-services-for-autoscale). It lets you easily configure conditions to automatically scale instances horizontally. Some services have limited or no built-in functionality to automatically scale in, so be sure to document these cases and define processes to deal with scaling in.

Many Azure services offer APIs that you can use to design custom automatic scaling operations using [Azure Automation](/azure/automation/overview), such as the code samples at [Autoscale your Azure IoT Hub](/samples/azure-samples/iot-hub-dotnet-autoscale/iot-hub-dotnet-autoscale/). You can use tools like KEDA for event-driven autoscaling, which is available in [Azure Kubernetes Service](/azure/aks/intro-kubernetes) and [Azure Container Apps](/azure/container-apps/overview).

[Azure Monitor autoscale](/azure/azure-monitor/autoscale/autoscale-get-started) provides a common set of autoscaling functionality for Azure Virtual Machine Scale Sets, Azure App Service, and Azure Cloud Services. Scaling can be performed on a schedule or based on a runtime metric, such as CPU or memory usage. See the [Azure Monitor guide](/azure/azure-monitor/best-practices-plan) for best practices.

## Tradeoffs

Scaling up has cost implications, so optimize your strategy to scale down as soon as possible to help keep costs under control. Ensure that the automation you're employing to scale up also has triggers to scale down.

### Autoscaling considerations

Autoscaling isn't an instant solution. Simply adding resources to a system or running more instances of a process doesn't guarantee that the performance of the system will improve. Consider the following points when designing an autoscaling strategy:

The system must be designed to be horizontally scalable. Avoid making assumptions about instance affinity. Don't design solutions that require that the code is always running in a specific instance of a process. When scaling a cloud service or website horizontally, don't assume that a series of requests from the same source are always routed to the same instance. For the same reason, design services to be stateless to avoid requiring a series of requests from an application to always be routed to the same instance of a service. When designing a service that reads messages from a queue and processes them, don't make any assumptions about which instance of the service handles a specific message. Autoscaling could start more instances of a service as the queue length grows. The [Competing Consumers pattern](/azure/architecture/patterns/competing-consumers) describes how to handle this scenario.

If the solution implements a long-running task, design this task to support both scaling out and scaling in. Without due care, such a task could prevent an instance of a process from being shut down cleanly when the system scales in. Or, it could lose data if the process forcibly terminates. Ideally, refactor a long-running task and break up the processing that it performs into smaller, discrete chunks. The [Pipes and Filters pattern](/azure/architecture/patterns/pipes-and-filters) provides an example of how you can achieve this solution.

Alternatively, you can implement a checkpoint mechanism that records state information about the task at regular intervals. You can then save this state in durable storage that can be accessed by any instance of the process running the task. In this way, if the process is shut down, the work that it was performing can be resumed from the last checkpoint by another instance. There are libraries that provide this functionality, such as NServiceBus and MassTransit. They transparently persist state, where the intervals are aligned with the processing of messages from queues in [Azure Service Bus](/azure/service-bus-messaging/compare-messaging-services#azure-service-bus).

When background tasks run on separate compute instances, such as in worker roles of a cloud-services–hosted application, you might need to scale different parts of the application by using different scaling policies. For example, you might need to deploy extra user interface (UI) compute instances without increasing the number of background compute instances, or vice-versa. If you offer different levels of service, such as basic and premium service packages, you might need to scale out the compute resources for premium service packages more aggressively than for basic service packages to meet service-level agreements (SLAs).

Consider the length of the queue over which UI and background compute instances communicate. Use it as a criterion for your autoscaling strategy. This issue is one possible indicator of an imbalance or difference between the current load and the processing capacity of the background task. A slightly more complex but better attribute on which to base scaling decisions is to use the time between when a message was sent and when its processing was complete. This interval is known as the critical time. If this critical time value is below a meaningful business threshold, then it's unnecessary to scale, even if the queue length is long.

For example, there could be 50,000 messages in a queue. But the critical time of the oldest message is 500 ms, and the endpoint is dealing with integration with a third-party web service for sending out emails. Business stakeholders would likely consider that to be a period of time that wouldn't justify spending extra money for scaling.

On the other hand, there could be 500 messages in a queue, with the same 500-ms critical time, but the endpoint is part of the critical path in some real-time online game where business stakeholders defined a response time of 100 ms or less. In that case, scaling out would make sense.

To use critical time in autoscaling decisions, it's helpful to have a library automatically add the relevant information to the headers of messages while they're sent and processed. One library that provides this functionality is [NServiceBus](https://docs.particular.net/monitoring/metrics/definitions#metrics-captured-critical-time).

If you base your autoscaling strategy on counters that measure business processes, such as the number of orders placed per hour or the average running time of a complex transaction, ensure that you fully understand the relationship between the results from these types of counters and the actual compute capacity requirements. It might be necessary to scale more than one component or compute unit in response to changes in business process counters.

To prevent a system from attempting to scale out excessively, and to avoid the costs associated with running many thousands of instances, consider limiting the maximum number of instances that are automatically added. Most autoscaling mechanisms let you specify the minimum and maximum number of instances for a rule. In addition, consider gracefully degrading the functionality that the system provides if the maximum number of instances have been deployed and the system is still overloaded.

Keep in mind that autoscaling might not be the most appropriate mechanism to handle a sudden burst in a workload. It takes time to provision and start new instances of a service or add resources to a system, and the peak demand might pass by the time these extra resources are available. In this scenario, it might be better to throttle the service. For more information, see the [Throttling pattern](/azure/architecture/patterns/throttling).

Conversely, if you need the capacity to process all requests when the volume fluctuates rapidly, and cost isn't a major contributing factor, consider using an aggressive autoscaling strategy that starts more instances more quickly. You can also use a scheduled policy that starts a sufficient number of instances to meet the maximum load before that load is expected.

The autoscaling mechanism should monitor the autoscaling process and log the details of each autoscaling event (what triggered it, what resources were added or removed, and when). If you create a custom autoscaling mechanism, ensure that it incorporates this capability. Analyze the information to help measure the effectiveness of the autoscaling strategy, and tune it if necessary. You can tune both in the short term, as the usage patterns become more obvious, and over the long term, as the business expands or the requirements of the application evolve. If an application reaches the upper limit defined for autoscaling, the mechanism might also alert an operator who could manually start more resources if necessary. Under these circumstances, the operator might also be responsible for manually removing these resources after the workload eases.

## Example

Refer to the AKS baseline reference architecture [scaling guidance](/azure/architecture/reference-architectures/containers/aks/baseline-aks#node-and-pod-scalability).

## Related links

- [Performance efficiency scaling](/azure/well-architected/scalability/principles)
- [Best practices for autoscale](/azure/azure-monitor/autoscale/autoscale-best-practices#choose-the-thresholds-carefully-for-all-metric-types)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 
