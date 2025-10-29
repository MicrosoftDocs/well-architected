---
title: Architecture strategies for designing a reliable scaling strategy 
description: Learn about recommendations for designing a reliable scaling strategy, including Azure facilitation and tradeoff considerations.
author: claytonsiemens77
ms.author: csiemens
ms.date: 05/29/2025
ms.topic: concept-article
---

# Architecture strategies for designing a reliable scaling strategy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:06**|Implement a timely and reliable scaling strategy at the application, data, and infrastructure levels. Base the scaling strategy on actual or predicted usage patterns and minimize manual intervention. |
|---|---|

This guide describes the recommendations for designing a reliable scaling strategy.

**Definitions**

| Term | Definition |
| --------- | ---------------- |
| Vertical scaling | A scaling approach that adds compute capacity to existing resources.|
| Horizontal scaling | A scaling approach that adds instances of a given type of resource.|
| Autoscaling | A scaling approach that automatically adds or removes resources when a set of conditions is met.   |

> [!NOTE]
> Scaling operations can be static (regularly scheduled daily scaling to accommodate normal load patterns), automatic (an automated process in response to conditions being met), or manual (an operator performs a one-time scaling operation in reaction to an unanticipated load change). Both vertical and horizontal scaling can be performed via any of these methods. However, automatic vertical scaling requires additional custom automation development and can cause downtime depending on the resources being scaled.

The system must be designed to be horizontally scalable. Avoid making assumptions about instance affinity. Don't design solutions that require that the code is always running in a specific instance of a process. When scaling a cloud service or website horizontally, don't assume that a series of requests from the same source are always routed to the same instance. For the same reason, design services to be stateless to avoid requiring a series of requests from an application to always be routed to the same instance of a service. When designing a service that reads messages from a queue and processes them, don't make any assumptions about which instance of the service handles a specific message. Autoscaling could start more instances of a service as the queue length grows. The [Competing Consumers pattern](/azure/architecture/patterns/competing-consumers) describes how to handle this scenario.

To use critical time in autoscaling decisions, it's helpful to have a library automatically add the relevant information to the headers of messages while they're sent and processed. One library that provides this functionality is [NServiceBus](https://docs.particular.net/monitoring/metrics/definitions#metrics-captured-critical-time).


## Design according to load patterns

To design a reliable scaling strategy for your workloads, focus on identifying load patterns for the user and system flows for each workload that leads to a scaling operation. Here are examples of different load patterns and their corresponding scaling strategies:

- *Static:* Every night by 11 PM EST, the number of active users of your application falls below 100, and the CPU utilization for the app servers drops by 90% across all nodes. To handle this, you can schedule the scale down of your compute nodes to the minimum count (2) between 11 PM and 6 AM EST.

- *Dynamic, Regular, and Predictable:* Every Monday morning, 1000 employees across multiple regions sign in to the ERP system. To manage this, you can schedule the scale-out of your compute nodes to the normal daily capacity before the first region starts work.

- *Dynamic, Irregular, and Predictable:* A product launch happens on the first day of the month, and there's historical data from previous launches on how the traffic increases in these situations. To address this, you can define a one-time scheduled scale-up of your compute and database instances on the morning of a product launch, and scale back down after one week.

- *Dynamic, Irregular, and Unpredictable:* A large-scale event causes a spike in demand for a product. For example, companies manufacturing and selling dehumidifiers can experience a sudden surge in traffic after a hurricane or other flooding event when people in affected areas need to dry rooms in their homes. To handle this, you can set autoscale thresholds to account for unplanned traffic spikes.

## Adapt scaling strategies to fit individual components or flows

There's no one-size-fits-all scaling strategy. Different cloud services have different degrees of support of scaling and different approaches to scaling. For that reason, it's important to understand how scaling is supported and implemented across all of your workload components to design your overall scaling strategy. You might apply scaling strategies at the individual component level or at the flow level, depending on your architectural design. When determining how you'll implement scaling across your workload, consider these factors:

- **Those components that can't be scaled out**. An example would be large, relational databases that don't have sharding enabled and can't be refactored without significant impact. Document the resource limits published by your cloud provider and monitor those resources closely. Include those specific resources in your future planning for migrating to scalable services.

- **The relationship of the flow’s components in terms of order of scale operations**. Ensure that you don’t inadvertently overload a downstream component by scaling an upstream component first.

- **Any stateful application elements that might be interrupted by a scaling operation and any session affinity (or session stickiness) that's implemented**. Stickiness can limit your scaling ability and introduces single points of failure. Design your workload to be stateless to the extent practical.

- **Potential bottlenecks**. Scaling out doesn't fix every performance issue. For example, if your backend database is the bottleneck, it doesn't help to add more web servers. Identify and resolve the bottlenecks in the system first before just adding more instances. Stateful parts of the system are the most likely cause of bottlenecks.

- **Handling long-running tasks**.  Design a long-running task to support both scaling out and scaling in. Without due care, such a task could prevent an instance of a process from being shut down cleanly when the system scales in. Or, it could lose data if the process forcibly terminates. Ideally, refactor a long-running task and break up the processing that it performs into smaller, discrete chunks. The [Pipes and Filters pattern](/azure/architecture/patterns/pipes-and-filters) provides an example of how you can achieve this solution.

## Choose the right technology

Making well-informed technology choices with scaling in mind will help you ensure that your workload can meet your reliability targets as your workload evolves. Research the scaling abilities offered for different resources that offer similar functionality and choose the best combination for your future growth plans. For example, you might have several options for data stores that can host the particular kind of databases you'll use. However, one choice might have better scaling functionality out-of-the-box than others, which could make it a better choice for your workload.

- **Take advantage of services that automatically scale.** When practical, use SaaS services that automatically scale without any configuration or input. Global services like [Microsoft Entra ID](/azure/active-directory/fundamentals/whatis) offer this functionality. [Serverless solutions](https://azure.microsoft.com/solutions/serverless/) also offer automatic scaling and can be good choices for many use cases.

- **Take advantage of services that offer out-of-the-box scaling.** Many PaaS services offer integrated, easy-to-use scaling features that you can configure to meet your reliability requirements. For example, you can configure [throughput scaling for Cosmos DB](/azure/cosmos-db/provision-throughput-autoscale) to meet your particular requirements.

## Automate scaling 

Automate the scaling operations for your workload components to extent practical. When using resources that have configurable autoscaling functionality, build the configuration logic into your infrastructure-as-code (IaC) deployment code. When using resources that don't have offer autoscaling out-of-the-box, build automation to perform scaling operations using native automation tooling and include the automation code in your IaC code.

If you base your autoscaling strategy on counters that measure business processes, such as the number of orders placed per hour or the average running time of a complex transaction, ensure that you fully understand the relationship between the results from these types of counters and the actual compute capacity requirements. It might be necessary to scale more than one component or compute unit in response to changes in business process counters.


Keep in mind that autoscaling might not be the most appropriate mechanism to handle a sudden burst in a workload. It takes time to provision and start new instances of a service or add resources to a system, and the peak demand might pass by the time these extra resources are available. In this scenario, it might be better to throttle the service. For more information, see the [Throttling pattern](/azure/architecture/patterns/throttling).

Conversely, if you need the capacity to process all requests when the volume fluctuates rapidly, and cost isn't a major contributing factor, consider using an aggressive autoscaling strategy that starts more instances more quickly. You can also use a scheduled policy that starts a sufficient number of instances to meet the maximum load before that load is expected.

## Choose appropriate scale units

Base your scaling strategy on scale units, which are the logical grouping of components to scale together and the scale increments to use (like moving from one VM SKU to another). Options to consider are:

- **Scaling resources individually**: You might need to only scale individual VMs or databases.

- **Scaling a full component at the same time:** For example, you might have a microservice API that is composed of an App Service, database, and queues that need to scale simultaneously.

- **Scaling the full solution**: For complex or mission critical workloads, scaling the entire solution as a [deployment stamp](/azure/architecture/patterns/deployment-stamp) can simplify your scaling strategy. Rather than managing the scaling schedules and autoscaling thresholds of many distinct resources, you can apply a limited set of scaling definitions to a deployment stamp and then mirror that across stamps as needed.

> [!IMPORTANT]
> Set a maximum limit on the number of scale units that can be automatically allocated to avoid excess costs.

## Optimize the scale unit initialization time

When designing your scaling strategy, keep in mind that different services scale at different timescales. There are some services that scale near-instantaneously and others that scale much slower. For example, API Management instances can take up to 45 minutes to finish their scaling operations. To account for the scaling operation's timescale, properly plan to perform the scaling operation before the expected increased load hits your workload. Other recommendations to consider include:

- Pre-initialize nodes that will be deployed to reduce the time required for initialization.

- Allow for a buffer time around configuration changes before you make further changes or use the system in an uncommon way. For example, you might deallocate an App Gateway backend instance via a rule change. You'll need to wait for connections to drain from that instance before it can be safely removed.

- Over-provision resources to handle increased load while scaling takes place. You might ensure that VMs normally run at 75% utilization capacity to ensure that they can handle increased load while horizontal scaling takes place.

- Fine-tune your scaling thresholds with monitoring. Use your capacity monitoring to ensure that your scaling thresholds to trigger scaling operations.

## Scale data stores using sharding and partitioning

Optimize the reliability of your data estate by including it in your scaling strategy. Partitioning data spreads a database across logical or physical storage resources, removing single points of failure. Choose the best partitioning strategy for your use case.

- **Horizontal partitioning (sharding)**: Partitions (shards) are placed in separate data stores, but all partitions have the same schema. Each shard holds a subset of the database. This is a good approach to optimize reliability as it helps facilitate load balancing and minimizes the burden on operations when dealing with issues. Shards can be replicated for higher reliability.

- **Vertical partitioning**: Each partition holds a subset of the fields for items in the data store. The fields are divided according to their usage pattern.

- **Functional partitioning**: Data is aggregated according to how each bounded context in the system uses the data. 

Consider combining these strategies when you design a partitioning scheme. For example, you might divide data into shards and then use vertical partitioning to further subdivide the data in each shard.

**Optimize your partition strategy for scalability**. Analyze data access patterns to determine which operations require the most processing resources and balance your partitions to ensure that each one has enough resources to handle scalability requirements.

For detailed guidance on partitioning and sharding, see the [design guide](../design-guides/partition-data.md)

## Monitor your scaling operations

The autoscaling mechanism should monitor the autoscaling process and log the details of each autoscaling event (what triggered it, what resources were added or removed, and when). If you create a custom autoscaling mechanism, ensure that it incorporates this capability. Proactively analyze the information to help measure the effectiveness of the autoscaling strategy, and tune it if necessary.

## Azure facilitation

An autoscaling feature is available in [many Azure services](/azure/azure-monitor/autoscale/autoscale-overview#supported-services-for-autoscale). It lets you easily configure conditions to automatically scale instances horizontally. Some services have limited or no built-in functionality to automatically scale in, so be sure to document these cases and define processes to deal with scaling in.

Many Azure services offer APIs that you can use to design custom automatic scaling operations using [Azure Automation](/azure/automation/overview), such as the code samples at [Autoscale your Azure IoT Hub](/samples/azure-samples/iot-hub-dotnet-autoscale/iot-hub-dotnet-autoscale/). You can use tools like KEDA for event-driven autoscaling, which is available in [Azure Kubernetes Service](/azure/aks/intro-kubernetes) and [Azure Container Apps](/azure/container-apps/overview).

[Azure Monitor autoscale](/azure/azure-monitor/autoscale/autoscale-get-started) provides a common set of autoscaling functionality for Azure Virtual Machine Scale Sets, Azure App Service, Azure Spring Apps and more. Scaling can be performed on a schedule or based on a runtime metric, such as CPU or memory usage. See the [Azure Monitor guide](/azure/azure-monitor/best-practices-plan) for best practices.

## Tradeoffs

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Scaling up has cost implications, so optimize your strategy to scale down as soon as possible to help keep costs under control. Ensure that the automation you're employing to scale up also has triggers to scale down.

## Example

Refer to the AKS baseline reference architecture [scaling guidance](/azure/architecture/reference-architectures/containers/aks/baseline-aks#node-and-pod-scalability).

## Related links

- [Performance efficiency scaling](/azure/well-architected/scalability/principles)
- [Best practices for autoscale](/azure/azure-monitor/autoscale/autoscale-best-practices#choose-the-thresholds-carefully-for-all-metric-types)

## Reliability checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Reliability checklist](checklist.md)
