---
title: Design applications for performance
description: Review application performance efficiency design considerations, including storage, databases, VM sizes, microservices architecture, and connection pooling.
author: martinekuan
ms.author: martinek
ms.date: 05/01/2023
ms.topic: conceptual
ms.custom:
  - How are you designing your applications for performance?
  - article
---

# Design applications for performance

Application design is critical to ensuring performance efficiency as load increases. This article gives you insights on the most important aspects of designing applications for performance. For more performance efficiency design related articles, see:

- [Design applications for efficiency](design-efficiency.md)
- [Design applications for scale](design-scale.md)
- [Design applications for capacity](design-capacity.md)

<a name="choose-the-right-data-storage"></a>
## Choose the right storage

The overall design of the storage tier can greatly affect an application's performance and scalability. The Azure Storage platform is designed to be massively scalable to meet the data storage and performance needs of modern applications.

Data services in the Azure Storage platform include:

- [Azure Blob](/azure/storage/blobs/storage-blobs-introduction), a massively scalable object store for text and binary data, which includes support for big data analytics through [Azure Data Lake Analytics](https://azure.microsoft.com/products/data-lake-analytics).
- [Azure Files](/azure/storage/files/storage-files-introduction), managed file shares for cloud or on-premises deployments.
- [Azure Queue](/azure/storage/queues/storage-queues-introduction), a messaging store for reliable messaging between application components.
- [Azure Tables](/azure/storage/tables/table-storage-overview), a NoSQL store for schemaless storage of structured data.
- [Azure Disks](/azure/virtual-machines/managed-disks-overview), block-level storage volumes for Azure virtual machines (VMs).

Each service is accessed through an Azure Storage account. To get started, see [Create a storage account](/azure/storage/common/storage-account-create?tabs=azure-portal).

Most cloud workloads adopt the *polyglot* persistence approach. Instead of one data store service, a mix of technologies is used. Your application might require more than one type of data store. For guidance about when to use different storage types, see [Sample scenarios for Azure Storage services](/azure/storage/common/storage-introduction#sample-scenarios-for-azure-storage-services).

## Choose the right databases

The choice of database can affect an application's performance and scalability. Database reads and writes involve network calls and storage input and output (I/O), both of which are expensive. Choosing the right database service to store and retrieve data is therefore critical to ensure application performance. Azure has many database services to fit most needs. There are also third-party options from [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) that can be considered.

To help choose a database type, determine whether the application's storage requirements fit a relational design (SQL), or a key-value, document, or graph design (NoSQL). Some applications might use both a SQL and a NoSQL database for different storage needs.

For a detailed description of NoSQL and relational databases, see [Understand distributed NoSQL databases](/azure/cosmos-db/relational-nosql). See [Select an Azure data store for your application](/azure/architecture/guide/technology-choices/data-store-decision-tree) to help find appropriate managed data storage solutions.

### Relational databases

Use a relational database when strong consistency guarantees are important. All changes are atomic, and transactions always leave the data in a consistent state. A relational database can't usually scale out horizontally without sharding the data, and implementing manual sharding can be time consuming. Also, the data in relational database must be normalized, which isn't appropriate for every data set.

If a relational database is optimal, Azure offers several platform as a service (PaaS) options that fully manage database hosting and operations. Azure SQL can host single databases or multiple databases through Azure SQL Managed Instance. Azure database offerings span performance, scale, size, reliability, disaster recovery, and migration compatibility requirements. Azure offers the following PaaS relational database services:

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [Azure Database for MariaDB](https://azure.microsoft.com/services/mariadb)

### NoSQL databases

Use a NoSQL database when application performance and availability are more important than strong consistency. NoSQL databases are ideal for handling large, unrelated, indeterminate, or rapidly changing data. NoSQL databases also have tradeoffs. For more information, see [Understand distributed NoSQL database challenges](/azure/cosmos-db/relational-nosql#challenges).

Azure provides two managed services that optimize for NoSQL solutions, [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) and [Azure Cache for Redis](https://azure.microsoft.com/services/cache). For document and graph databases, Azure Cosmos DB offers extreme scale and performance.

## Choose the right VM sizes

Choose VM sizing requirements based on your needs. Choosing the wrong VM size can result in capacity issues as VMs approach their limits, or can lead to unnecessary costs.

To choose the right VM size, consider your workloads, number of CPUs, RAM capacity, disk size, and speed according to business requirements. For more information about Azure VM sizes and their purposes, see [Sizes for virtual machines in Azure](/azure/virtual-machines/sizes).

Azure offers the following categories of VM sizes, each designed to run different workloads.

- [General-purpose](/azure/virtual-machines/sizes-general) provide balanced CPU-to-memory ratio ideal for testing and development, small to medium databases, and low to medium traffic web servers.
- [Memory optimized](/azure/virtual-machines/sizes-memory) offer a high memory-to-CPU ratio good for relational database servers, medium to large caches, and in-memory analytics.
- [Compute optimized](/azure/virtual-machines/sizes-compute) have a high CPU-to-memory ratio good for medium traffic web servers, network appliances, batch processes, and application servers.
- [GPU optimized](/azure/virtual-machines/sizes-gpu) are available with single, multiple, or fractional GPUs designed for compute-intensive, graphics-intensive, and visualization workloads.
- [High performance compute](/azure/virtual-machines/sizes-hpc) are designed to deliver leadership-class performance, scalability, and cost efficiency for various real-world high performance computing (HPC) workloads.
- [Storage optimized](/azure/virtual-machines/sizes-storage) offer high disk throughput and I/O ideal for big data, SQL and NoSQL databases, data warehousing, and large transactional databases. Example databases include Cassandra, MongoDB, Cloudera, and Redis.

## Use microservices architectures

Microservices are a popular architectural style for building applications that are resilient, highly scalable, independently deployable, and able to evolve quickly. A microservices architecture consists of a collection of small, autonomous services. Each service is self-contained and implements a single business capability.

Breaking up larger entities into small discrete pieces doesn't ensure sizing and scaling capabilities. Application logic must be written to control these capabilities. One benefit of microservices is that they can be scaled independently. Independent scaling lets you scale out subsystems that require more resources without scaling the entire application.

Another microservices benefit is fault isolation. If an individual microservice becomes unavailable, it doesn't disrupt the entire application. Upstream microservices should be designed to handle faults correctly, for example by implementing circuit breaking. To learn more about the benefits of microservices, see [Microservices architecture design benefits](/azure/architecture/microservices/#benefits).

Building with microservices has challenges for development and testing. Writing a small service that relies on other dependent services requires a different approach than writing a traditional monolithic or layered application. Existing tools aren't always designed to work with service dependencies. Refactoring across service boundaries can be difficult. It's also challenging to test service dependencies, especially when the application is evolving quickly. For more information about the potential drawbacks of a microservices architecture, see [Microservices architecture design challenges](/azure/architecture/microservices/#challenges).

### Dynamic service discovery for microservices applications

In a microservices application, many separate services or instances of services in play must receive instructions on who to contact and other configuration information. Hard coding this information is flawed. Through service discovery, a service instance can spin up and dynamically discover the configuration information it needs to become functional without having the information hard coded.

Use an orchestration platform designed to execute and manage individual services, such as Kubernetes or Service Fabric. Individual services can be right sized, scaled up, scaled down, and dynamically configured to match user demand. An orchestrator like Azure Kubernetes Service (AKS) or Azure Service Fabric can pack a higher density of services onto a single host, which allows for more efficient resource utilization.

Both platforms provide built-in services for executing, scaling, and operating a microservices architecture. These services include discovery and finding where a particular service is running. AKS supports pod autoscaling and cluster autoscaling. For more information, see [Advanced AKS microservices architecture autoscaling](/azure/architecture/reference-architectures/containers/aks-microservices/aks-microservices-advanced#autoscaling). Service Fabric architecture takes a different approach to scaling for stateless and stateful services. For more information, see [Azure Service Fabric scaling services](/azure/architecture/reference-architectures/microservices/service-fabric#scaling-services).

> [!TIP]
> Decomposing an application into microservices is a level of decoupling that is an architectural best practice when appropriate. A microservices architecture can also bring some challenges. The design patterns in [Design patterns for microservices](/azure/architecture/microservices/design/patterns) can help mitigate these challenges.

## Use connection pooling

Establishing a connection to a database is an expensive operation that requires creating an authenticated network connection to the remote database server. Database connections are especially expensive for applications that open new connections frequently. Connection pooling reuses existing connections and avoids the expense of opening a new connection for each request. Connection pooling reduces connection latency and enables higher database throughput (transactions per second) on the server.

### Pool size limits

Azure limits the number of network connections for a VM or Azure App Service instance. Exceeding this limit causes connections to be slowed down or terminated. With connection pooling, a fixed set of connections is established at startup time and maintained. In many cases, a default pool size might consist only of a small handful of connections that perform quickly in basic test scenarios.

The default pool size might become a bottleneck under scale when the pool is exhausted. It's a best practice to establish a pool size that maps to the number of concurrent transactions supported on each application instance.

Each database and application platform has slightly different requirements for the right way to set up and use the pool. For a .NET code example that uses SQL Server and Azure SQL Database, see [SQL Server connection pooling](/dotnet/framework/data/adonet/sql-server-connection-pooling). In all cases, testing is important to ensure a connection pool is properly established and works as designed under load.

> [!TIP]
> Choose a pool size that uses the same number of concurrent connections. Choose a size that can handle more than the existing connections so you can quickly handle a new incoming request.

### Integrated security and connection pools

Integrated security is a single unified solution that uses a set of common policies and configuration settings to protect every service that a business runs. Integrated security reduces scaling, provisioning, and management issues, including higher costs and complexity, and increases control and overall security.

However, sometimes you might not want to use connection pooling for security reasons. For example, although connection pooling improves the performance of subsequent database requests for a single user, that user can't take advantage of connections made by other users. Connection pooling also results in at least one connection per user to the database server.

Measure your business' security requirements against the advantages and disadvantages of connection pooling. For more information, see [Pool fragmentation](/dotnet/framework/data/adonet/sql-server-connection-pooling#pool-fragmentation).

## Next steps

> [!div class="nextstepaction"]
> [Design for efficiency](design-efficiency.md)
