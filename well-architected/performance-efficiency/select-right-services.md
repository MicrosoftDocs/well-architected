---
title: Recommendations for selecting resources
description: Learn best practices for selecting the right resources.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for selecting the right resources: PE 03

This guide describes the best practices for selecting the right resources. Resources include infrastructure, Azure services, and SKUs. These three resources are foundational decisions for deploying a workload in Azure. They determine the physical hardware and specifications a workload runs on and where. Failure to select infrastructure, services, and SKUs carefully could cause performance issues and require major rearchitecting efforts in the future.

## Definitions 

|  Term|                                 Definition|
|-|-|
 |  Infrastructure                       |The physical components of cloud computing, such as datacenters, networking, and hardware, and their location in the world.|
  |Azure service|                        A product or offering provided by Microsoft Azure. Examples include Azure App Service, Azure Storage, and Azure SQL Database.|
|  Azure resource|                       A single entity or component that you can provision, configure, and utilize within Azure. Examples include web apps, storage accounts, and relational databases.|
|  Stock keeping unit (SKU)|             A tier in an Azure service that provides unique capabilities.|
|  Region|                               A collection of datacenters that share a common perimeter that Microsoft defines based on latency.|
|  Availability zone|                    A grouping of one or more datacenters within a region that has availability zones.|
|  Platform-as-a-service (PaaS)|         Azure services where Microsoft is responsible for the operating system and shares responsibility with customers for managing identity, applications, and networking.|
|  Infrastructure-as-a-service (IaaS) |  Azure services where the customer is responsible for the operating system, identity, applications, and networking.|
|  Compute services                     |Azure services that provide the infrastructure you need to run applications.|
|  Storage services                     |Azure services for object, block, and file storage.|
|Database services|                    Azure services for relational and non-relational databases for your application.|
 
## Your responsibility

**Consider performance targets.** Select services that allow you to meet performance targets. They should allow you to scale and partition per your design. You also need to select services that emit performance telemetry you can measure. You should be able to collect performance data on all components supporting critical flows.

**Align design choices to team skill sets.** The Azure services you use should align with technology your workload team knows how to use. For example, different compute platforms, such as Azure Kubernetes Service, Azure App Service, and Azure Spring App, and database types require different skills to run and maintain. However, new technologies might provide significant performance gains. In these cases, you need to invest in training the workload team to adopt the new technology.

**Consider service limits when selecting services.** Azure services and SKUs have capacity limits. When workload resources or data hits hard service limits, it requires rearchitecting or migrating your workload to a different solution. To avoid hitting those limits, you should pick resources that allow you to grow.

**Consider service limits when architecting workloads.** Some Azure resources have rate limits and enforce request throttling. These restrictions can limit your performance throughput. Sometimes you can pick an alternative service to mitigate the restrictions. Other times, you need to architect around these limitations. For example, Key Vault has [transaction rate limits](/azure/key-vault/general/service-limits) that restrict the number of concurrent calls it can receive. It returns an HTTP status code 429 when you [exceed its threshold](/azure/key-vault/general/overview-throttling). Based on the service limits, you shouldn't put Key Vault in the HTTP request flow.

**Know organizational restrictions.** Your organization might have a limited list of services they allow you to deploy. You need to be aware of these restrictions before you design a solution.

**Test all changes.** Changing services or location can affect the performance of the workload. For example, a web app running on Azure App Service in Region X might performance differently than the same web app running on Azure App Service in Region Y. You need to run performance tests on all service and infrastructure changes. It's ideal to test before deploying the changes. A proof-of-concept can help generate the performance data you need.

**Know compliance requirements.** Compliance requirements can have a significant effect on the services, SKUs, and infrastructure you select. They might impose requirements on storage, encryption, access controls, audit logs, and locations. You need to select services, SKUs, and infrastructure that allows you to meet all compliance requirements.

**Offload functionality** **to** **platform.** Offloading functionality from custom code in your workload to the cloud platform can significantly improve the performance efficiency of a workload. Platform features are usually optimized for performance and regularly maintained. Many features are built to scale. You should choose services that allow you to offload functionality to platform features. You should only develop custom code when platform features aren't sufficient. Custom code adds complexity and makes the workload more prone to performance issues.

### Infrastructure selection

**Consider available services.** Different regions in Azure have different features available. These features include the number of availability zones, Azure services, SKUs, and customer base. You need to ensure a region meets workload performance needs across these features. You can relocate resources in Azure. Over time, you might want to move your workload or a portion of it to maximize performance. For more information, see [Choose the Right Azure Region](https://azure.microsoft.com/explore/global-infrastructure/geographies/#overview) and Azure Products by Region.

**Consider latency.** Communicating across regions adds latency to a workload. You should place workload components that communicate frequently in the same region. This can also be a factor across availability zones in latency-sensitive workloads.

**Understand availability zone mapping to subscriptions.** Availability zones are abstractions, not physical things. Availability zones aren't rigidly mapped physical datacenters. Availability zones only map to physical datacenters within a subscription. In a different subscription, the availability zones map to different physical datacenters. For example, \`zone 1\` in \`subscription A\` might not map the same datacenter(s) as \`zone 1\` in \`subscription B\`. For multi-subscription workloads, you need to consider the zone mapping between regions and choose the availability zones that maximize performance.

### Network requirements

**Consider networking.** Different services and SKUs allow you to interact with a virtual network in different ways. Some SKUs allow you to inject or integrate a resource with a virtual network. Virtual network integration and injection provide private communication between resources in the virtual network. Other services and SKUs only support private endpoints for secure communication. SKUs can also offer different routing options. For example, AKS has SKUs that allow you to configure different network routing options. Some can significantly improve network throughput benefits. You should pick the services and SKUs that support your networking preferences.

### Compute selection

**Choose the right compute service.** The right Azure compute services for your workload depend on several factors. These factors include the technology stack, goals for the workload, current state of the workload, and skills your team has. You need evaluate these factors to pick the right compute service for your workload. For more information, see [Choose an Azure compute service.](/azure/architecture/guide/technology-choices/compute-decision-tree)

**Choose the right SKU.** For compute services, SKUs provide different compute performance, features, and capabilities. You need to use your performance targets to pick the right SKU for your compute service.

**Select the minimum instance count.** Some workloads at minimal load require more than one instance of a compute resource. You should set a minimum instance count to meet your minimal load performance needs.

### Database selection

**Understand databases.** The choice of databases can affect an application\'s performance. Database reads and writes involve network calls and storage input and output (I/O). Both are performance intensive. Choosing the right database service to store and retrieve data is therefore critical to ensure application performance.

-   **Understand relational databases.** You should use a relational database when strong consistency guarantees are important. All changes are atomic, and transactions always leave the data in a consistent state. A relational database can\'t usually scale out horizontally without sharding the data. Implementing manual sharding can be time consuming. You need to normalize the data in relational database. Normalization isn\'t appropriate for every data set.

-   **Understand non-relational databases.** Non-relational databases are also known as NoSQL databases. NoSQL databases are ideal for handling large, unrelated, indeterminate, or rapidly changing data. NoSQL databases also have tradeoffs. You should use a non-relational database when application performance and availability are more important than strong consistency. For more information, see [Understand distributed NoSQL database challenges](/azure/cosmos-db/relational-nosql#challenges).

**Choose the right database.** You need to figure out what database type your workload needs. The application storage requirements fit a relational design (SQL), or a key-value, document, or graph design (NoSQL). Some applications might use both a SQL and a NoSQL database for different storage needs. You need to use your performance targets to pick the right SKU for your database. For more information, see [Data store decision tree.](/azure/architecture/guide/technology-choices/data-store-decision-tree)

### Cache selection

**Select the right caching solution.** A cache stores frequently accessed data to improve performance. Caching reduces data access latency and lowers the load on data storage components. It allows the workload to handle more requests without scaling. The two primary use cases are caching workload data and static content. A Redis cache can store session data, database results, API responses, and reference data (configuration settings). A content delivery network or static web app can cache and serve static content. You should consider how caching can improve your workload performance and select the correct solution for your use case.

### Storage selection

**Use the right storage services.** The overall design of the storage tier can greatly affect an application\'s performance. You need to review Azure storage options and select the ones that best support your workload. For more information, see [Choose an Azure storage service](/azure/architecture/guide/technology-choices/storage-options) and [Sample scenarios for Azure Storage services](/azure/storage/common/storage-introduction#sample-scenarios-for-azure-storage-services).

**Pick the right storage account type.** Azure has several storage account types. For more information, see [Types of storage accounts.](/azure/storage/common/storage-account-overview#types-of-storage-accounts)

**Consider multiple storage services.** Most cloud workloads use a mix of storage technologies. It's known as the polyglot persistence approach. You need to figure out the right combination of storage services for your workload. You might also want to separate data to avoid contamination. For example, you could have separate storage accounts for monitoring data and business data.

## Azure facilitation 

-   Azure has a global infrastructure with regions around the world, and edge point of presence.

-   Azure has features that allow you to move Azure services to different regions.

-   Azure has many platform-as-a-service (PaaS) solutions for compute, database, and storage.

-   Azure has many virtual machine options to run workloads or workload components.

-   Azure has a content delivery network called Front Door. It has a Redis cache called Azure Cache for Redis. Azure also provides Azure Application Gateway.

-   Performance metrics surfaced by most services.

-   Many resource and SKU choices. Multiple services to accomplish the same outcome, but that can be selected to align to your team's skill set or have optimizations for specific use cases.

-   Offer offloading many cross-cutting concerns to highly performant platform features, but also supports you brining your own custom solutions.

## Tradeoff

-   The best service might be a technology that your team isn't skilled in yet.

-   The best service might be a service that you cannot afford.

-   The best service might require additional layers to support security requirements. For example, a public load balancer might fit your performance needs, but if it lacks a web application firewall, you might have to deploy a firewall to secure the workload.
