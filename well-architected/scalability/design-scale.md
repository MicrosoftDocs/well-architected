---
title: Design applications for scaling
description: Examine the scaling options for performance efficiency. Services covered by Azure Autoscale can scale automatically to match demand to accommodate workload.
author: martinekuan
ms.author: martinek
ms.date: 12/08/2021
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-efficiency
ms.custom:
  - references_regions
products:
  - azure
categories:
  - management-and-governance
---

# Design applications for scaling

*Scalability*, an important aspect of performance efficiency, is the ability of a system to resize to handle changing load. This article describes the types of scaling, how to plan for growth, and how to use platform autoscaling features to manage load.

To achieve scalability, consider how your application scales, and implement platform as a service (PaaS) offerings that have built-in scaling operations. Azure services that *autoscale* can scale automatically to match demand to workload. For example, these services scale out to ensure capacity during workload peaks and return to normal automatically when the peak drops. For more information about autoscaling, see [Use autoscaling to manage load](#use-autoscaling-to-manage-load).

## Understand horizontal and vertical scaling

Two ways an application can scale include *vertical scaling* and *horizontal scaling*. Vertical scaling, or scaling up, increases the capacity of a resource, for example by using a larger virtual machine (VM) size. Horizontal scaling, or scaling out, adds new instances of a resource, such as VMs or database replicas.

An advantage of vertical scaling is that you can scale up without changing the application. But at some point, you hit a limit where you can't scale up anymore. Any further scaling must then be horizontal.

Horizontal scaling has the following significant advantages over vertical scaling:

- **True cloud scale applications can be designed** to run on hundreds or even thousands of nodes, reaching scales that aren't possible on a single node.
- **Horizontal scale is elastic**, so you can add more instances if load increases, or remove instances during quieter periods.
- **Scaling out can be triggered automatically**, either on a schedule or in response to changes in load.
- **Scaling out might be cheaper than scaling up**, because running several small VMs can cost less than running a single large VM.
- **Horizontal scaling can improve reliability** by adding redundancy. If an instance goes down, the application keeps running.

Horizontal scale must be designed into the system. For example, you can scale out VMs by placing them behind a load balancer. But each VM in the pool must handle any client request, so the application must be stateless or store state externally, as in a distributed cache. Managed PaaS services often have horizontal scaling and autoscaling built in. The ease of scaling is a major advantage of using PaaS services.

Simply adding more instances doesn't mean an application can scale. Adding more instances could push a bottleneck somewhere else. For example, scaling a web front end to handle more client requests might trigger lock contentions in the database. You need to consider measures such as optimistic concurrency or data partitioning to enable more throughput to the database.

Always conduct performance and load testing to find potential bottlenecks. Resolving one bottleneck might reveal other bottlenecks elsewhere. The stateful parts of a system, such as databases, are the most common cause of bottlenecks, and require careful design to scale horizontally.

## Plan for growth

Use the [Performance efficiency checklist](performance-efficiency.md) to review your design from a scalability standpoint. For more information about how to determine the upper and maximum limits of an application's capacity, see [Performance testing](performance-test.md).

In the cloud, the ability to take advantage of scalability depends on your infrastructure and services. Platforms like Kubernetes were built with scaling in mind. VMs might not scale as easily, although scale operations are possible. With VMs, you might want to plan ahead to avoid scaling infrastructure in the future. Another option is to select a different platform such as Azure Virtual Machine Scale Sets.

Planning for growth starts with understanding your current workloads, which can help you anticipate scaling needs based on predictive usage scenarios. An example of a predictive usage scenario is an e-commerce site that recognizes that its infrastructure should scale appropriately for an expected high volume of holiday traffic.

With scalable platforms, you can predict the current average and peak times for your workload and manage this prediction with payment plans. You pay either per minute or per hour depending on the service.

Carry out load tests and stress tests to determine the necessary infrastructure to support predicted spikes in workloads. A good plan includes incorporating a buffer to accommodate random spikes.

Another critical component of planning for scale is to make sure the region that host your application supports the necessary capacity to accommodate the load increase. If you use a multiregion architecture, make sure the secondary regions can also support the increase. A region might offer a product, but might not have the necessary SKUs to support the predicted load increase, so you need to verify capacity. To verify your regions and available SKUs, first select the product and regions in [Products available by region](https://azure.microsoft.com/explore/global-infrastructure/products-by-region). Then, use the Azure portal to check the SKUs that are available.

![Screenshot of the Azure.com Products available by region page.](../_images/design-scale-1a.png)

### Determine scale units

For each resource, know the upper scaling limits, and use [sharding](/azure/azure-sql/database/elastic-scale-introduction#sharding) or decomposition if you need to go beyond those limits. Design the application so that it's easily scaled by adding one or more scale units, such as by using the [deployment stamps pattern](/azure/architecture/patterns/deployment-stamp).

Use built-in scaling features or tools to understand which resources need to scale concurrently with other resources. Then use well-defined sets of resources to determine the scale units for the system. For example, adding *X* number of front-end VMs might require *Y* number of extra queues and *Z* number of storage accounts to handle the extra workload. So a scale unit could consist of *X* VM instances, *Y* queues, and *Z* storage accounts.

## Use autoscaling to manage load

Autoscaling lets you automatically run the right amount of resources to handle your app load. Autoscaling adds resources, or scales out, to handle increases in load such as seasonal workloads. Autoscaling also removes idle resources, or scales in, to save money during decreases in load, such as nights and weekends for some corporate apps. Autoscaling automatically scales between the minimum and maximum number of instances to run, and automatically adds or removes VMs based on a set of rules.

![Diagram that illustrates autoscaling.](../_images/design-autoscale.png)

### Evaluate scaling delay

Horizontal scaling changes the number of identical instances, and vertical scaling switches to more or less powerful instances. Scaling operations can be fast, but they take time to complete. It's important to understand how the delay affects the application under load, and whether degraded performance is acceptable.

For more information, see [Best practices for autoscale](/azure/azure-monitor/platform/autoscale-best-practices#choose-the-thresholds-carefully-for-all-metric-types).

### Use platform autoscaling features

To get the greatest benefit from autoscaling, follow these practices:

- Use built-in autoscaling features when possible, rather than custom or third-party mechanisms.
- Use scheduled scaling rules where possible, to ensure that resources are available.
- Add reactive autoscaling to the rules where appropriate, to cope with unexpected changes in demand.

> [!NOTE]
> If your application is explicitly designed to handle the termination of some of its instances, use autoscaling to reduce operational costs. Scale down and scale in resources that are no longer necessary for the load.

For more information, see [Autoscaling](/azure/architecture/best-practices/auto-scaling).

### Autoscale compute or memory-intensive applications

CPU or memory-intensive applications can require scaling up to a larger machine SKU with more CPU or memory. Once the demand for CPU or memory decreases, instances can revert back to the original size.

For example, you might have an application that processes images, videos, or music. While scaling out allows the system to process more files simultaneously, it doesn't improve the processing speed for each individual file. Given the process and requirements, it might make sense to scale up a server by adding CPU or memory to quickly process a large media file.

### Use Azure services that autoscale

Autoscaling works by collecting resource metrics like CPU and memory usage, and application metrics like requests queued and requests per second. Rules can then be created to add and remove instances depending on how the rule evaluates.

An [Azure App Service](/azure/app-service/overview-hosting-plans#how-does-my-app-run-and-scale) plan allows autoscale rules to be set for scale out/scale in and scale up/scale down. The [App Service autoscaling sample](https://github.com/mspnp/samples/tree/master/PerformanceEfficiency/AppServiceAutoscalingSample) shows how to create an Azure App Service plan, which includes an Azure App Service.

Scaling also applies to [Azure Automation](/azure/automation/automation-intro).

[Azure Kubernetes Service](/azure/aks/intro-kubernetes) (AKS) offers two levels of autoscale:

- **Horizontal autoscale** can be enabled on service containers to add more or fewer pod instances within the cluster.
- **Cluster autoscale** can be enabled on the agent VM instances running an agent node pool to add or remove VM instances dynamically.

The following Azure services also offer autoscaling capability:

- [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) with virtual machine scale sets offers autoscale capabilities for true infrastructure as a service (IaaS) scenarios.
- [Azure Application Gateway](/azure/application-gateway/overview) and [Azure API Management](/azure/api-management/api-management-key-concepts) are PaaS offerings for ingress services that enable autoscale.
- [Azure Functions](/azure/azure-functions/functions-overview), [Azure Logic Apps](/azure/logic-apps/logic-apps-overview), and [Azure App Services](/azure/app-service/overview) provide serverless pay-per-use consumption modeling that inherently provides autoscaling capabilities.
- [Azure SQL Database](/archive/blogs/sqlserverstorageengine/azure-sql-database-scalability) is a PaaS platform that changes performance characteristics of a database on the fly and assigns more resources when needed or releases them when not needed. SQL Database allows [scaling up/down](/archive/blogs/sqlserverstorageengine/azure-sql-database-scalability#scaling-updown), [read scale-out](/archive/blogs/sqlserverstorageengine/azure-sql-database-scalability#read-scale-out), and [global scale-out/sharding](/archive/blogs/sqlserverstorageengine/azure-sql-database-scalability#global-scale-outsharding) capabilities.

Each service documents its autoscale capabilities. Review [Autoscale overview](/azure/azure-monitor/platform/autoscale-overview) for a general discussion about Azure platform autoscaling.

> [!NOTE]
> If your application doesn't have built-in autoscale ability, or isn't configured to scale out automatically as load increases, its services might fail if they become saturated with user requests. For possible solutions, see [Azure Automation](/azure/virtual-desktop/set-up-scaling-script).

## Next steps

> [!div class="nextstepaction"]
> [Plan for capacity](design-capacity.md)
