---
title: Recommendations for selecting resources
description: Learn best practices for selecting the right resources.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for selecting the right services: PE 03

This guide describes the best practices for selecting the right services for your workload. Selecting the right services is about choosing the appropriate cloud services that best meet the requirements and demands of your specific workload. By choosing services that are designed to handle the workload's requirements, you can ensure that your workload meets the performance targets. On the other hand, if you pick the wrong services for your workload. The services may not be able to handle the workload's demands, leading to slow response times, bottlenecks, or even workload failures.

**Definitions**

|  Term| Definition|
|---|---|
|  Infrastructure |The physical components of cloud computing, such as datacenters, networking, and hardware, and their location in the world.|
|Service|A product or offering from a cloud service provider. Examples include Azure App Service, Azure Storage, and Azure SQL Database.|
|Resource|A single entity or component that you can create, configure, and utilize within a cloud service provider. Examples include web apps, storage accounts, and relational databases.|
|Stock keeping unit (SKU)|A tier in a service that provides unique capabilities.|
|Region|A collection of datacenters that share a common perimeter.|
|Availability zone| A grouping of one or more datacenters within a region that has availability zones.|
|Platform-as-a-service (PaaS)| Services where the cloud service provider is responsible for the operating system and shares responsibility with customers for managing identity, applications, and networking.|
|Infrastructure-as-a-service (IaaS) | Services where the customer is responsible for the operating system, identity, applications, and networking.|
|Compute services| Services that provide the infrastructure you need to run applications.|
|Storage services| Services for object, block, and file storage.|
|Database services| Services for relational and nonrelational databases for your application.|

## Key strategies

Selecting the right services means aligning services with the specific requirements and demands of your workload. The selection must account for factors such as resource utilization, scalability, and latency. By choosing the appropriate services, you can ensure that your workload runs efficiently, maximizes resource utilization, and delivers optimal performance.

### Understand workload requirements

Understanding workload requirements means to evaluate the specific needs and characteristics of a workload. It involves aligning workload requirements to performance targets at every tier. You must account for specific constraints or dependencies. By understanding workload requirements, you can make informed decisions and take appropriate actions to select the right infrastructure and implement strategies to handle peak loads or variations in demand.

**Meet performance targets.** Select services that allow you to meet performance targets for your workload. Make sure the service has the capability to support the performance needs and allows you to monitor its performance. Collect performance data on critical components.

**Meet organizational restrictions.** Be aware of any restrictions your organization may have on services you're allowed to deploy. Consider these restrictions while designing your solution.

**Meet compliance and security requirements.** Compliance and security requirements can affect the services and configurations you select. Ensure that the services you choose meet the requirements related to storage, encryption, access controls, audit logs, and data locations.

**Meet team skills.** Workloads require personnel to build and maintain them. Different services require different personnel skills to use properly. Pick services your team knows how to use, or commit to training them before selecting it. Ensure the members of a team have the expertise and knowledge to effectively use the selected services to optimize its performance.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Specialized services offer specific functionalities but might limit customization. More flexible resources require more management and configuration. Managed services offer ease of management, but you might have less control over underlying infrastructure compared to self-managed resources.

### Understand the services

Understanding services before choosing them involves considering various factors and gaining a comprehensive understanding of the services. Research and assess the various services and tools offered by the cloud provider. Find the ones that best align with your workload requirements. Consider factors like managed services, serverless options, and specialized services for different workload types.

**Understand service limits.** Service limits are the predefined thresholds or boundaries set by a service provider that define the maximum usage of resources or capabilities within that service. By knowing the service limits, you can avoid issues such as resource contention, performance degradation, or unexpected service interruptions. You plan and scale their infrastructure appropriately, taking into account factors such as data volume, processing capacity, and data residency requirements.

**Offload functionality.** Service features are designed to handle specific tasks efficiently and at scale, allowing you to take advantage of its optimized infrastructure. They're also regularly maintained. Choose services that allow you to offload functionality to service features instead of writing and maintaining your own custom code. Custom code adds complexity and makes the workload more prone to performance issues. Only develop custom code when service features aren't sufficient.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** The best service for your workload might be a technology that your team isn't skilled, can't afford, or requires extra security layers to support. For example, a public load balancer might fit your performance needs. But if you don't already have a web application firewall, you might have to deploy a firewall to secure the workload.

### Evaluate the location

Choosing the right infrastructure refers to selecting the appropriate region and availability zones for hosting your workload. The region refers to a specific geographic location, while availability zones are separate physical data centers within a region. By choosing the right infrastructure, you ensure that your workload runs on a robust and efficient infrastructure that can meet your performance and availability requirements. Different regions and availability zones may have varying levels of network latency and compute resources. By selecting the infrastructure with low latency and ample resources, you can optimize the performance of your workload.

**Understand available features.** Different regions have different features available. These features include the number of services and availability zones. Understand the features available in a region before selecting it. Ensure a region meets workload performance needs.

**Consider latency.** Communicating across regions and availability zones adds latency to a workload. You should place workload components that communicate frequently in the same region. For a workload that's sensitive to latency workloads, you should consider. Additionally, select a region that is close to your users to minimize network latency and provide a better user experience.

**Understand datacenter mapping.** Availability zones don't always map to the same datacenters. Availability zones only map to the same datacenters within a subscription. For example, \`zone 1\` in \`subscription A\` might not map to the same datacenter(s) as \`zone 1\` in \`subscription B\`. For multi-subscription workloads, you need to consider the zone mapping between regions and choose the availability zones that maximize performance.

### Evaluate networking requirements

Evaluating networking requirements is the process of assessing network needs to determine the appropriate workload services and configurations. The goal is to ensure that the network can support the workload. To evaluate networking requirements, consider the following factors:

- *Network traffic:* Assess the expected network traffic for the workload. Understand the data transfer needs and the frequency of network requests.
- *Bandwidth requirements:* Determine the bandwidth requirements for the workload. Consider the amount of data that needs to be transmitted and received over the network.
- *Latency:* Evaluate the desired latency for the workload. Latency refers to the time it takes for data to travel from the source to the destination. Lower latency can be achieved by using private virtual networks and backbone networks instead of traversing the public internet.
- *Throughput:* Consider the required throughput for the workload. Throughput refers to the amount of data that can be transmitted over a network in a given time. Configuring network routing options can significantly improve network throughput benefits.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Private virtual networking limits public access and make it more difficult to deploy and manage resources.

### Evaluate compute requirements

Choosing the right compute service is important for performance efficiency because different compute services have varying capabilities and characteristics that can affect the performance of your workload. By selecting the optimal compute service, you can ensure that your workload runs efficiently.

*Understand Different instance types are optimized for different workloads, such as CPU-optimized, memory-optimized, and GPU instances. Choose the instance type that aligns with your specific needs.
**Consider autoscaling.** If your workload has variable demand, consider a compute service with autoscaling that can automatically adjust the compute capacity based on demand. Autoscaling helps ensure that you have enough resources during peak times and avoid over-provisioning during low demand periods.

**Consider containerization.** Containers have become a provide performance advantages over a noncontainerized workload. Consider using containerization if it suits your architectural needs. Containers improve compute performance in several ways:

- *Isolation:* Containers provide isolated environments for applications, ensuring that application resources don't interfere with another. This isolation ensures that compute resources assigned to a container are dedicated to running that specific application, resulting in optimal performance.
- *Resource efficiency:* Containers are lightweight and share the host operating system's kernel, allowing for efficient resource utilization. As a result, multiple containers can run on the same virtualized infrastructure, maximizing the use of compute resources.
- *Fast startup time:* Container images are prebuilt and can be quickly started when needed. This fast startup time enables rapid scalability, allowing applications to scale up or down based on demand and avoid performance bottlenecks.
- *Portability:* Containers encapsulate all the required dependencies and libraries within the image, making it easier to move applications across different operating systems or environments. This portability enables flexibility in deploying applications and allows for easy migration between cloud providers or on-premises environments.

When using containers, it's important to consider design considerations such as containerizing all application components. Use Linux-based container runtimes for lightweight images and make containers immutable and replaceable with short lifecycles. Additionally, gathering relevant logs and metrics from containers, container hosts, and the underlying cluster is essential for monitoring and performance analysis. It's worth noting that containers are just one component of an overall architecture, and the choice of container orchestrators, like Kubernetes, can further enhance performance and scalability.

**Choose the right tier.** Within each compute service, you can set the compute capacity, select features, and enable certain capabilities. Use your performance targets to pi the right service tier for your compute service.

**Select instance count.** Determine the minimum instance count required for your workload. Some workloads, even at minimal load, may require more than one instance of a compute resource to meet performance needs. Set the minimum instance count accordingly.

### Evaluate load balancing requirements

Load balancing ensures that network traffic is distributed evenly, preventing any single server from becoming overwhelmed with requests. It helps maintain optimal performance by preventing bottlenecks and reducing response times. Evaluate the different load balancing services available in your cloud provider's offerings. Use the provided documentation and comparison tools to understand the features and select the most suitable service for your specific use case. To select a load balancing service, consider the following factors:

- *Traffic type:* Determine whether the load balancing service needs to handle web traffic (HTTP/HTTPS) or other protocols such as TCP or UDP.
- *Global or regional: Decide whether you require load balancing within a specific region or across multiple regions.
- *Service level objectives:* Consider the service-level agreement (SLA). Different load balancing services offer different levels of performance.
- *Features:* Consider load balancing services that provide site acceleration, optimal traffic distribution, and low-latency Layer 4 load balancing.

### Evaluate database requirements

The choice of database can affect factors such as data storage and retrieval, transaction processing, consistency guarantees, and handling of large or rapidly changing data. Assess the specific needs and criteria for your database and select a database system that can meet those requirements effectively. Evaluate database requirements before choosing a database. To evaluate database requirements and choose the right database, you can follow these steps:

- *Identify workload needs:* Understand the specific requirements of your workload, such as data volume, expected transaction rates, concurrency, data types, and expected growth. These requirements form the basis for evaluating different database systems. For example, if your workload requires high-performance real-time data processing, you might choose a database system that is optimized for fast data ingestion and low latency.
- *Consider data model:* Determine the data model that best suits your workload. You need to evaluate database requirements to ensure that the chosen database supports the required data structures, relationships, and integrity constraints. For example, if your data has a highly relational structure, you might opt for a relational database management system (RDBMS) that provides robust support for transactions and referential integrity. It could be hierarchical, network, relational, object-oriented, or NoSQL. Assess the complexity of your data model and ensure that the chosen database supports the required data structures and relationships.
- *Evaluate capabilities:* Consider factors such as read/write patterns, query complexity, latency requirements, and scalability needs. Evaluate the performance capabilities of different database systems accordingly. Some databases excel in read-heavy workloads, while others are optimized for write-intensive or analytical workloads.
- *Assess load:* Consider factors such as data volume, transaction rates, read/write ratios, and expected growth. Choosing a database that can handle the anticipated workload ensures smooth operation and prevents performance bottlenecks as your workload scales. Consider the scalability requirements of your workload. These requirements include anticipated data growth, concurrent user access, and the need for horizontal or vertical scaling. Evaluate the scalability options and availability features provided by different database systems.

### Evaluate cache requirements

A cache stores frequently accessed data to improve performance. Caching reduces data access latency and lowers the load on data storage components. It allows the workload to handle more requests without scaling. The two primary use cases are caching workload data and static content. A Redis cache can store session data, database results, API responses, and reference data (configuration settings). A content delivery network or static web app can cache and serve static content. You should consider how caching can improve your workload performance and select the correct solution for your use case.

### Evaluate storage requirements

Choose storage services that align with your data access patterns, durability requirements, and performance needs. Most cloud workloads use a mix of storage technologies. It's known as the polyglot persistence approach. You need to figure out the right combination of storage services for your workload. You might also want to separate data to avoid contamination. For example, you could have separate storage accounts for monitoring data and business data.

## Azure facilitation

**Understand requirements:** Azure Monitor helps you collect and analyze data from your workload. It provides insights into the performance and health of your workloads, allowing you to identify and troubleshoot issues.

**Understand services:** Azure had detailed documentation on all it [services and products](/azure/) to determine if they meet your performance requirements. Azure offers multiple services to accomplish the same outcome. It gives you the flexibility to align service choices to your performance needs, team's skill set, and cost requirement.

**Understand service limits:** For a list of the most common Azure limits, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits). The [ResourceLimits](https://github.com/mspnp/samples/tree/master/OperationalExcellence/ResourceLimits) sample shows how to query the limits and quotas for commonly used resources.

**Choose the right services:** Azure has a range of services to accommodate any workload. Azure provides [service selection guidance](/azure/architecture/guide/technology-choices/technology-choices-overview) for each service type. The guidance helps streamline your selection based on your requirements:

- [Choose the right region](https://azure.microsoft.com/explore/global-infrastructure/geographies/#choose-your-region)
- [Compute services](/azure/architecture/guide/technology-choices/compute-decision-tree)
- [Container services](/azure/container-apps/compare-options)
- [Data store services](/azure/architecture/guide/technology-choices/data-store-overview)
- [Storage services](/azure/architecture/guide/technology-choices/storage-options)
- [Load balancing services](/azure/architecture/guide/technology-choices/load-balancing-overview)
