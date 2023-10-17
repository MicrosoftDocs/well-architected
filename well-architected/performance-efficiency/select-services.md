---
title: Select appropriate services
description: Learn how to select the appropriate services that best meet the requirements and demands of your workload.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for selecting appropriate services

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:**

|[PE:03](checklist.md)| Select the right services. The services, infrastructure, and tier selections must support your ability to reach the workload's performance targets and accommodate expected capacity changes. The selections should also weigh the benefits of using platform features or building a custom implementation.|
|---|---|

This guide describes the recommendations for selecting appropriate services for your workload. The following recommendations help you choose services that best meet the requirements and demands of your workload. When you use services that are designed to handle your workload's requirements, you can ensure that your workload meets your performance targets. If you choose inappropriate services for your workload, the services might not be capable of handling your workload's demands. Insufficient services can lead to slow response times, bottlenecks, or workload failures.

**Definitions**

|  Term| Definition|
|---|---|
|Availability zone| [A separated group of datacenters within a region](/azure/reliability/availability-zones-overview). Each availability zone is independent of the others, with its own power, cooling, and networking infrastructure. [Many regions support availability zones](/azure/reliability/availability-zones-service-support#azure-regions-with-availability-zone-support).|
|Compute service| A service that provides the infrastructure that you need to run an application.|
|Database service| A service that provides relational and nonrelational databases for your application.|
|  Infrastructure |The physical components of cloud computing, and the geographic location of the components.|
|Infrastructure as a service (IaaS) | A service in which the customer is responsible for the operating system, identity, applications, and networking.|
|Platform as a service (PaaS)| A service in which the cloud service provider is responsible for the operating system. The cloud service provider shares responsibility with the customer for managing identity, applications, and networking. |
|Region|A geographic perimeter that contains a set of datacenters.|
|Resource|A single entity or component that you can create, configure, and utilize within a cloud service provider. |
|Service|A product or offering from a cloud service provider. |
| SKU | A tier of a service that provides unique capabilities.|
|Storage service| A service that provides storage for objects, blocks, and files.|

## Key design strategies

The services you choose should align with your workload's performance targets and be adaptable to future capacity needs. As the workload expands or evolves, the services you use should match your performance standards without requiring major adjustments. Consider the balance between platform features and custom implementations. Platform features provide immediate solutions, but custom-built options offer precise tailoring. Your service selections should be both forward-thinking and tailored to your specific needs, taking into account the trade-offs between convenience and customization.

### Understand workload requirements

Understanding workload requirements refers to grasping the technical and functional demands of a workload. This analysis helps determine the resources, storage, compute, network, and other specifications needed to run the workload. Aligning services with the specific needs of a workload helps prevent overprovisioning or underutilizing resources.

Evaluate the needs and characteristics of your workload to determine the requirements, and align your workload requirements to your performance targets at every tier. You must account for constraints or dependencies. When you understand your workload requirements, you can make informed decisions. You can determine the right infrastructure and implement strategies to handle peak loads or variations in demand.

- *Meet performance targets.* Select services that enable you to meet the performance targets for your workload. Ensure that a service can support the performance needs and that you can monitor its performance. Collect performance data for critical components.

- *Consider organizational restrictions.* Be familiar with restrictions that your organization might have on services that you deploy. Consider these restrictions when you design your solution.

- *Consider compliance and security requirements.* Compliance and security requirements can affect services and configurations that you select. Ensure that a service you choose meets the requirements that are related to storage, encryption, access controls, audit logs, and data locations.

- *Consider team skills.* Your team builds and maintains workloads. Different services require different skills. Choose services that your team knows how to use, or commit to training them before you choose a service. Ensure that team members possess the expertise and knowledge to effectively use services and to optimize their performance.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Specialized services offer specific functionalities but might limit customization. Flexible resources require more management and configuration compared to specialized services. Managed services offer ease of management, but you might have less control over the underlying infrastructure compared to self-managed resources.

### Understand services

To understand services means to grasp the capabilities, limits, and functionalities of a vendor's tools and offerings. An understanding of services helps you use built-in features, reducing the need for complex custom solutions and improving performance efficiency.

Consider various factors and gain a comprehensive understanding of a service before you choose it. Research and assess services and tools that the provider offers. Determine which services and tools best align with your workload requirements. Consider factors like managed services, serverless options, and specialized services.

#### Understand service limits

Service limits are the predefined thresholds or boundaries that service providers set. Service limits define the maximum usage of resources or capabilities within that service. When you're familiar with service limits, you can avoid issues such as resource contention, performance degradation, or unexpected service interruptions. You can plan and scale the infrastructure appropriately, taking into account factors such as data volume, processing capacity, and data residency requirements.

#### Prefer platform features

Preferring platform features means to using built-in functionalities provided by a provider to handle specific tasks without custom code. Vendors design platform features to handle specific tasks efficiently at scale, and they regularly maintain these features. Platform features allow you to better take advantage of cloud infrastructure capabilities. Choose services that allow you to offload functionality to the platform instead of writing and maintaining your own custom code. In many cases, platform-as-a-service (PaaS) solutions provide better performance efficiency than custom code. Custom code adds complexity and makes the workload prone to performance issues. Only develop custom code when service features aren't sufficient.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: The best service for your workload might be a technology that your team isn't skilled at, can't afford, or it might require extra security layers. For example, a public load balancer might fit your performance needs. But if you don't have a web application firewall, you might have to deploy a firewall to secure the workload.

### Evaluate infrastructure requirements

The performance efficiency of services is tied to the infrastructure they reside on. It makes the selection of the right infrastructure paramount. Evaluating infrastructure requirements means identifying the geographical region and availability zones best suited to support your workload. Key considerations in this decision-making include:

- *Understand regions and availability zones.* Every region corresponds to a distinct geographic location. Availability zones represent individual physical datacenters within a given region.

- *Understand available features.* Different regions have different available features, such as the number of services and availability zones. Understand the features that are available in a region before you select it. Ensure that a region meets your workload performance needs.

- *Consider latency.* Latency, the time data takes to travel from source to destination, increases the further services are from each other. Services communicating across regions or availability zones can face increased latency. Identifying services that frequently communicate and positioning them within the same region is recommended. Additionally, selecting a region proximate to your primary user base can minimize latency, offering a better user experience.

- *Understand datacenter mapping.* Availability zones might not map consistently to the same datacenters across different subscriptions. For instance, 'Zone 1' in 'Subscription A' might be different from 'Zone 1' in 'Subscription B'. When operating with multiple subscriptions, you should know these mappings to select zones that bolster performance optimally.

### Evaluate networking requirements

Assess your network needs to determine the appropriate workload services and configurations. Ensure that the network can support your workload. To evaluate networking requirements, consider:

- **Network traffic**: Assess the expected network traffic for the workload. Understand the data transfer needs and the frequency of network requests.

- **Bandwidth requirements**: Determine the bandwidth requirements for the workload. Consider the amount of data that's transmitted and received over the network.

- **Latency**: Evaluate the desired latency for the workload. Use private virtual networks and backbone networks instead of traversing the public internet. This technique decreases the latency of the workload.

- **Throughput**: Consider the required throughput for the workload. Throughput refers to the amount of data that can be transmitted over a network in a given time. Configure the network routing options to take advantage of network throughput benefits.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Private virtual networking limits public access and makes it difficult to deploy and manage resources.

### Evaluate compute requirements

Choose the appropriate compute service to optimize performance efficiency. Different compute services have varying capabilities and characteristics that can affect the performance of your workload. Select the optimal compute service to ensure that your workload runs efficiently.

**Understand instance types.** Different instance types are optimized for different workloads, such as CPU-optimized, memory-optimized, and GPU instances. Choose the instance type that aligns with your needs.

**Consider automatic scaling.** If your workload has variable demand, consider a compute service with an autoscale feature that can automatically adjust the compute capacity based on demand. Automatically scaling helps ensure that you have enough resources during peak times and prevents overprovisioning during low demand periods.

**Consider containerization.** Containers provide performance advantages compared to a noncontainerized workload. Consider using containerization if it suits your architectural needs. Containers improve compute performance in several ways:

- *Isolation*: Containers provide isolated environments for applications. Containers ensure that application resources don't interfere with each other. This isolation ensures that compute resources that are assigned to a container are dedicated to running that specific application, which results in optimal performance.

- *Resource efficiency*: Containers are lightweight and share the host operating system's kernel, which allows for efficient resource utilization. Multiple containers can run on the same virtualized infrastructure, which maximizes the use of compute resources.

- *Fast startup time*: Container images are prebuilt and are quickly started when needed. This fast startup time enables rapid scalability. It allows applications to scale up or down based on demand and avoid performance bottlenecks.

- *Portability*: Containers encapsulate all the required dependencies and libraries within the image. With containers, it's easier to move applications across different operating systems or environments. This portability enables flexibility in deploying applications and allows for easy migration between cloud providers or on-premises environments.

When you use containers, consider design factors such as containerizing all application components. Use Linux-based container runtimes for lightweight images. Give containers short lifecycles to make them immutable and replaceable. Gather relevant logs and metrics from containers, container hosts, and the underlying cluster. Use this data to monitor and analyze performance. Containers are just one component of an overall architecture. Choose an appropriate container orchestrator, like Kubernetes, to further enhance performance and scalability.

**Choose the appropriate tier.** Within each compute service, you can set the compute capacity, select features, and enable capabilities. Based on your performance targets, choose the appropriate service tier for your compute service.

**Determine the instance count.** Determine the minimum instance count that your workload requires. Some workloads, even at minimal load, might require more than one instance of a compute resource. Set the minimum instance count accordingly.

### Evaluate load balancing requirements

Load balancing ensures that network traffic is distributed evenly and prevents any single server from being overwhelmed with requests. Load balancing helps prevent bottlenecks and reduce response times, which maintains optimal performance. Evaluate the different load balancing services that your cloud provider offers. Review the cloud provider's documentation and comparison tools to understand the features. Select the most suitable service for your workload. To select a load balancing service, consider:

- *Traffic type*: Determine whether the load balancing service needs to handle web traffic, like HTTP and HTTPS, or other protocols, such as Transmission Control Protocol (TCP) or User Datagram Protocol (UDP).

- *Global or regional*: Determine whether your workload requires load balancing within a specific region or across multiple regions.

- *Service-level objectives (SLOs)*: Consider the service-level agreement (SLA). Different load balancing services offer different levels of performance.

- *Features*: Consider load balancing services that provide site acceleration, optimal traffic distribution, and low-latency layer-4 load balancing.

### Evaluate database requirements

The database can affect factors such as data storage and retrieval, transaction processing, consistency guarantees, and handling of large or rapidly changing data. Assess the needs and criteria for your database. Select a database system that can meet those requirements. Evaluate the database requirements before you choose a database. To evaluate the database requirements and choose the appropriate database, follow these steps:

- **Identify the workload needs**. Understand the specific requirements of your workload, such as data volume, expected transaction rates, concurrency, data types, and expected growth. Evaluate different database systems based on your workload needs. For example, if your workload requires high-performance real-time data processing, you might choose a database system that's optimized for fast data ingestion and low latency.

- **Consider the data model**. Determine the data model that best suits your workload. Evaluate the database requirements to ensure that the chosen database supports the required data structures, relationships, and integrity constraints. For example, if your data has a highly relational structure, you might opt for a relational database management system (RDBMS) that provides robust support for transactions and referential integrity. The data model might be hierarchical, network, relational, object-oriented, or NoSQL. Assess the complexity of your data model. Ensure that the chosen database supports the required data structures and relationships.

- **Evaluate the capabilities**. Consider factors such as read/write patterns, query complexity, latency requirements, and scalability needs. Evaluate the performance capabilities of different database systems accordingly. Some databases excel in read-heavy workloads, while others are optimized for write-intensive or analytical workloads.

- **Assess the load**. Consider factors such as data volume, transaction rates, read/write ratios, and expected growth. Choose a database that can handle the anticipated workload to ensure smooth operation and prevent performance bottlenecks as your workload is scaled. Consider the scalability requirements of your workload. These requirements include anticipated data growth, concurrent user access, and the need for horizontal or vertical scaling. Evaluate the scalability options and availability features that different database systems provide.

### Evaluate cache requirements

A cache stores frequently accessed data. Caching reduces data access latency and lowers the load on data storage components. It allows the workload to handle more requests without scaling. It's common to cache workload data and static content. A Redis cache can store session data, database results, API responses, and reference data, such as configuration settings. A content delivery network or static web app can cache and serve static content. Consider caching data to improve your workload performance.

### Evaluate storage requirements

Choose storage services that align with your data access patterns, durability requirements, and performance needs. Most cloud workloads use a combination of storage technologies. This technique is known as the polyglot persistence approach. Determine the appropriate combination of storage services for your workload. You might also want to separate data to avoid contamination. For example, you might have separate storage accounts for monitoring data and business data.

## Azure facilitation

**Understand requirements.** Use Azure Monitor to collect and analyze data from your workload. Monitor provides insights into the performance and health of your workloads, allowing you to identify and troubleshoot issues.

**Understand services.** Review Azure [services and products](/azure/) to determine if they meet your performance requirements. Azure offers several services that accomplish the same outcome. You have the flexibility to align your choice of service to your performance needs, team skill sets, and cost requirements.

**Understand service limits.** For a list of the most common Azure limits, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits). The [ResourceLimits](https://github.com/mspnp/samples/tree/master/OperationalExcellence/ResourceLimits) sample shows how to query the limits and quotas for commonly used resources.

**Choose the appropriate services.** Azure has many services that can accommodate any workload. Review the [selection guidance](/azure/architecture/guide/technology-choices/technology-choices-overview) for each service type to help you streamline your selection based on your requirements. See the following guides to choose:

- [A region](https://azure.microsoft.com/explore/global-infrastructure/geographies/#choose-your-region)
- [Compute services](/azure/architecture/guide/technology-choices/compute-decision-tree)
- [Container services](/azure/container-apps/compare-options)
- [Data store services](/azure/architecture/guide/technology-choices/data-store-overview)
- [Load balancing services](/azure/architecture/guide/technology-choices/load-balancing-overview)
- [Storage services](/azure/architecture/guide/technology-choices/storage-options)

## Related links

- [Azure regions with availability zone support](/azure/reliability/availability-zones-service-support#azure-regions-with-availability-zone-support)
- [Recommendations for defining performance targets](performance-targets.md)
- [Recommendations for using availability zones and regions](../reliability/regions-availability-zones.md)
- [What are availability zones?](/azure/reliability/availability-zones-overview)

## Performance Efficiency checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
