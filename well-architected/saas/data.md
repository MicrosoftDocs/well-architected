---
title: Data for SaaS Workloads on Azure
description: Learn about the data platform considerations for data integrity and performance for SaaS workloads on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/05/2025
ms.topic: concept-article
ms.collection: learn-startups
---

# Data for SaaS workloads on Azure

Treat data as the most valuable asset of your solution. As an independent software vendor (ISV), you're responsible for managing your customers' data. Your data design strategy and choice of data store can significantly affect your customers.

This article provides guidance on how to ensure data integrity and confidentiality for your customers while meeting business performance requirements. It highlights key considerations to help you achieve these goals effectively.

## Select a data store

The data store in a software as a service (SaaS) solution affects its architecture, performance, reliability, and transactional complexity. Compare the capabilities of Azure managed services with similar non-Microsoft offerings. In some situations, you might also consider running open-source products on virtual machines.

### Design considerations

- **Differentiate between your transactional and analytical operations.** Transactional data stores are designed to support your applications, and analytical data stores are used for reporting and tasks like machine learning. These stores are built with specialized products and have unique needs for performance, access patterns, schemas, and use cases.

  This guide focuses on transactional data stores.

- **Understand your data needs.** Estimate the volume, frequency at which it can change, and type of data that you need to store.

  Expect data to grow significantly over time. For SaaS solutions, growth occurs in multiple dimensions. Anticipate increases in the volume and types of data as the number of customers grow. Make sure that you plan for that growth and invest in technologies that can support it.

  Decide between a relational or nonrelational data platform based on the nature of your data. For many transactional workloads, a relational database is a good option for modeling application entities as discrete tables. This approach allows queries to operate across the relational data model. Alternatively, if your data naturally fits a document model or follows a graph structure, a nonrelational approach might be more suitable.
 
  For more information, see [Relational versus NoSQL data platforms](/dotnet/architecture/cloud-native/relational-vs-nosql-data).

- **Minimize the types of data stores.** Storing different types of data in multiple, distinct data stores can be beneficial for mature organizations that have expertise across various data platforms. However, this approach often introduces unnecessary complexity for startups and smaller organizations. It's more effective to focus on one or a small number of data stores.
  
  If you don't have the business justification for using multiple data stores, then focus your efforts on one or a small number of data stores.

- **Use what you know, and invest in it.** If your team already has expertise with a specific data store, it's often better to use that data store instead of investing in learning new skills. Data stores and platforms are complex, and design decisions can be difficult to reverse.

  However, keep the potential growth in mind. If your current data store no longer meets your requirements, choose a data store that can enhance your solution's performance, resiliency, security, and operational efficiency. It should also help your team deepen their expertise.

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Separate transactional data stores for day-to-day operations from analytical and reporting data stores. | Mixing the intent of your data stores can lead to unnecessary complexity. Data segmentation enhances operational efficiency and maximizes the utilization of each data store. |
|Choose between a relational or nonrelational data structure based your requirements. Start with one or a small number of data stores. <br><br>Prioritize managed data stores. Common choices include Azure Cosmos DB, Azure SQL Database, MySQL, MongoDB, and PostgreSQL.|This approach helps minimize complexity and ensures that you use the right product to maximize efficiency. Managed data stores provide flexibility in managing resources and costs elastically and scale with your needs. Using managed data stores creates less management burden than deploying your own data store on your own infrastructure.|
|Invest in learning your chosen technology. Equip your team to manage the high scaling requirements and other complexities of SaaS solutions.| Learn about the tools that you use and their wider ecosystem so that you can effectively use your data platform as you scale.|
|Adopt flexibility in your data design.|As your SaaS solution grows or your requirements change, you can adapt by adding or changing data stores. This flexibility allows you to start with one data store and evolve over time to meet your needs.|

## Tenancy model and database strategy

A key aspect of data design is the decision to host resources on behalf of your customers or to host resources in their environment. Most SaaS providers host resources for all customers, which provides flexibility in database management. If you host resources in the customer's environment, consider how you access and manage those resources.

### Design considerations

- **Plan your database segmentation.** In business-to-business (B2B) SaaS solutions, we recommend that you create dedicated databases for each customer. This approach enhances data security by maintaining strict isolation between customers, which reduces the risk of data mixing and supports customer-managed encryption keys. It also helps you meet regulatory compliance requirements for some customers.

  Separating customer data into individual databases can improve performance by minimizing [noisy neighbor problems](/azure/architecture/antipatterns/noisy-neighbor/noisy-neighbor). Some managed data stores include resource allocation controls to mitigate these problems, provide cost efficiencies, and incorporate tools for managing multiple databases at scale.

  In some cases, it's appropriate to store multiple customers' data in a single data store. For example, in business-to-consumer (B2C) solutions, you can save data in a single store with logical partitioning based on customer ID. In B2B solutions that share components, you can use a single data store for specific parts, such as an event store, while ensuring that you include customer IDs on each event.

- **Collocate data stores with application components.** If you host resources on behalf of the customer, deploy in the same Azure region to avoid egress bandwidth charges and latency. When you host applications and data stores in a customer's environment, deploy them together in the same environment to avoid cross-environment complexities.
  
- **Standardize data store management as much as is practical.** Uniformity is key to managing data across customers. As your business grows, differences between customers increase risk and complexity. These differences can also make production outages more likely and troubleshooting more difficult.

  Avoid one-off changes in your management to support individual customers. For example, to support customer-managed metadata, avoid schema changes like adding extra columns to your database. Instead, build functionality for customers to add their own metadata. Similarly, if you need to provide different levels of database performance for different customers, create a single process that you can use to apply different configurations to different tiers of customers.
  
For more information about how your tenancy model affects your data strategy, see.

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Evaluate whether to share databases between multiple customers or provide a shared data platform. <br><br> Deploy a single database for each customer's data, where appropriate. Relax this strategy if strict isolation isn't a requirement, such as in B2C solutions.| This approach minimizes noisy neighbor problems and can support compliance requirements for some customers.|  
|Deploy applications and their databases in the same region.| This approach optimizes bandwidth cost and latency incurred by cross-region database access.|
|Design a standardized approach for storing customer-defined data or metadata. Avoid altering the schema for individual customers or causing customer environments to differ.|This approach helps you avoid the operational burden of managing inconsistencies between databases for each customer.|
|Plan for routine maintenance operations in the customer-deployed environment. <br><br>Plan how to access the database for updates, schema changes, maintenance, and other operations.|This proactive approach minimizes issues from lack of maintenance and reduces the risk of downtime and performance problems.|

## Plan capacity

Capacity planning refers to the management of resource utilization, with a focus on CPU, memory, storage, and disk operations like input and output operations per second (IOPS). Some data stores combine these resources into a simple, synthetic resource consumption metric, like a database throughput unit (DTU) in Azure SQL Database or a request unit (RU) in Azure Cosmos DB. Managed data stores provide flexibility in resource management, which allows changes over time. It's crucial to establish an initial plan and iterate as your needs evolve.

### Design considerations

- **Understand your resource allocation requirements.** Different customers in SaaS solutions might have varying resource needs. Smaller customers might require minimal resources, and larger customers might need more. Larger customers often pay more, which justifies higher resource allocation. By using separate databases for each customer, you can adjust resource allocation based on their size and needs.

- **Understand the different capacity models that data platforms offer.** Cloud-based data platforms often provide multiple resource models. For example, some Azure services like Azure Cosmos DB provide provisioned, throughput-based models and serverless models. Azure SQL Database also provides elastic pools.

  Provisioned throughput requires predetermined resource allocation, either from a single database or a group of databases. [Elastic pools](/azure/azure-sql/database/elastic-pool-overview) allow resource sharing among multiple databases. Elastic pools are commonly used in SaaS solutions.

  Even though provisioned throughput requires that you allocate resources ahead of time, services like Azure Cosmos DB provide [autoscale throughput](/azure/cosmos-db/provision-throughput-autoscale). You can set rules for dynamically adding or removing resources based on specified triggers.

  Serverless resource models automatically scale based on demand. This capability makes them a good starting point if you can't predict your capacity requirements ahead of time. However, they might support fewer features and become cost-inefficient as you grow. Serverless models are available in [Azure SQL Database](/azure/azure-sql/database/serverless-tier-overview) and [Azure Cosmos DB](/azure/cosmos-db/serverless).

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Model your database requirements for each customer. Determine whether you should have many small databases, fewer large databases, or a mixture of the two. <br><br>Use a t-shirt sizing exercise to categorize customers into small, medium, and large buckets.|This approach provides a rough estimate of resources needed per customer and helps you map customers to your billing model.|
|Segment resource pools based on the size of the customer databases that use them. <br><br> Use provisioned capacity to your advantage. For example, you might create a shared SQL elastic pool for smaller customers, a separate pool for medium customers, and dedicated resources for large customers.|By segmenting resource pools based on your customers' database size, you can optimize resource allocation and cost efficiency. |
|Take advantage of the built-in scaling capabilities that the managed services provide.|You can offload scaling responsibilities to the platform. Features like elastic pools and autoscale can help optimize resource use.|
|Regularly review serverless data stores to ensure that they continue to meet your needs.|You can ensure that the data store remains effective with your evolving needs. Optimize performance and cost-efficiency as your requirements change.|

## High availability and disaster recovery

Customers of SaaS solutions often have high expectations for high availability (HA) and disaster recovery (DR). If your customers operate in regulated industries or rely on your solution for daily operations, their requirements might be even more stringent.

HA and DR aren't one-size-fits-all solutions and depend on various factors. Have a clear understanding of the available options that are applicable to both you and your customers' requirements to make informed decisions about mitigating different risks.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Reliability, cost, and performance**: Resiliency for data services often requires distributing replicas or copies of your data across a wider geographic area to mitigate risks. However, there are tradeoffs. The longer the distance that data has to travel, the more protection you have against localized failures. But, copying data across longer distances increases latency and often costs more.
> Many managed data stores provide automated data replication, but they might impose limits on the types of replication that you can perform across different distances to maintain performance.

### Design considerations

- **Quantify reliability.** Measure your reliability requirements by using service-level objectives (SLOs), which include metrics like uptime, recovery time, and recovery point. These metrics are driven by both your business requirements and those of your customers, who may have varying needs. If you store large amounts of data on behalf of your customers, your HA and DR solution might need to be more complex to meet stringent requirements.

   For more information about reliability metrics, see [RE:04 Recommendations for defining reliability targets](../reliability/metrics.md).

- **Use platform features.** Azure provides capabilities for resiliency within a datacenter, within a region by using availability zones, and across a wider geographic area by using multiple regions. Combine strategies like availability zones, cross-region backup, and multiregion deployments to achieve the right level of resiliency and recoverability for your solution. For high resiliency requirements, consider a multiregion, active-passive architecture with asynchronous data replication between regions. This approach might result in some data loss during a catastrophic outage.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Multiregion, active-active designs with replication are the most resilient but are complex to build and test. For most active-active solutions, you need to design a conflict resolution approach that accounts for delays in data synchronization. Most solutions don't need this degree of resiliency.

  Refer to [Recommendations for using availability zones and regions](../design-guides/regions-availability-zones.md).

- **Use deployment stamps to isolate the blast radius of components.** The deployment stamps pattern is widely used in SaaS solutions because it provides benefits for deployment, management, performance, and reliability. For instance, deploying one stamp in the United States and another stamp in Europe ensures that customers in one region are isolated from outages in the other region and can operate independently.

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Focus on reliability requirements while you think about the overall data requirements for your and your customers.|By grounding your design decisions in those requirements, you can ensure that you make the appropriate tradeoffs and avoid under- or over-engineering for your needs.| 
|Reflect varying levels of reliability in your billing model. <br><br> Set expectations with your customers. Zero data loss during catastrophic outages or 100% uptime might be unrealistic.| Billing models can help your customers understand how much guaranteed reliability they're signing up for. For example, with a lower tier, customers get minimal guarantees. In a higher tier, customers receive more reliability because you can afford to replicate their resources across multiple regions.|
|Use Azure availability zones for production solutions. When possible, use zone redundant data stores.|Availability zones provide resiliency against datacenter outages, without significantly increasing the cost, latency, or complexity of your solution.|
|Keep backups of your data stores in a globally redundant format by using cross-region replication where available.|Cross-region backups of data add an extra level of reliability.|
|Use deployment stamps to create separate instances of your solution in geographically distributed locations.|By using deployment stamps to create separate instances of your solution in geographically distributed locations, you can increase resiliency and provide more benefits, like easier operations management.|
|Evaluate if you need multiregion deployment and if you need an active-active design to meet the requirements. <br><br> Consider the tradeoffs that are involved. Stateless components are easier to replicate than stateful components like a data store. |Spreading your solution or stamps across regions provides higher levels of resiliency by replicating data between regions.|

## Security and compliance

You're responsible for ensuring the confidentiality and integrity of your customers' data. As you build a security baseline, consider your security requirements and promises. Plan to meet your customers' compliance needs, including data retention.

### Design considerations

- **Networking:** Consider who will access your data store. Typically, only your application needs direct communication, so configure it for private-only networking.

- **Identity:** Consider how your data stores will be accessed. Many SaaS solutions use a single application identity for all data stores, with the application tier enforcing isolation and authorization. For row-level security or database-level authorization, you might need to propagate the user's identity to the data store, which is complex in a multitenant environment.

- **Data retention:** Plan your data retention policies in advance. Maintaining more data increases storage costs and management complexity. For instance, large amounts of data in a transactional database can complicate querying and truncating processes.

  For long-term data retention, such as for compliance or future analyses, consider relocating data to a store that's suitable for long-term retention.

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Configure your data stores to use private endpoints, and disable public endpoints.|This approach enhances security by restricting access to your internal network. By restricting access, you can reduce the risk of unauthorized access and potential data breaches.|
|Use managed identities and Microsoft Entra ID for authentication. Avoid the use of database keys or credentials.|Managed identities eliminate the need for database keys or credentials, which reduces the risk of credential theft and simplifies access management.|
|When you work with shared data stores, ensure that the application scopes all requests to a single tenant. For example, include the tenant identifier in `WHERE` clauses.|This process helps mitigate the risk of cross-tenant data leakage or impersonation.|
|Plan your data retention strategy based on compliance and business needs. Avoid keeping unnecessary historical data. For long-term retention, move data from primary stores to archival storage.|By avoiding unnecessary retention, you maintain a smaller surface area.|
|Use data store features to support your data lifecycle needs.<br><br>In Azure Cosmos DB, set the [time to live](/azure/cosmos-db/time-to-live) on documents. In Azure SQL, implement a sliding window strategy by using [table partitioning](/sql/relational-databases/partitions/partitioned-tables-and-indexes) to minimize the impact of the archival process on the database.|These approaches ensure efficient data lifecycle management, optimize storage by archiving or deleting outdated data, and reduce manual intervention when possible.|

## Operations

SaaS solutions often include a large number of databases or other stores. It's important to plan for routine maintenance on your fleet and explore automation options to manage these tasks efficiently.

### Design considerations

- **Understand your team's capabilities.** If you don't have large teams of database administrators who can perform detailed analyses on individual customers' databases, have a plan to perform operations at scale, and use platform tooling whenever possible.

- **Plan your regular maintenance procedures.** List the regular maintenance operations needed and their frequency. The specific operations vary based on the type of data store that you use. For example:
  - Monitor the total amount of data and data that's located in specific entities, like important tables.
  - Rebuild indexes.
  - Create or remove indexes based on changing query patterns.
  - Rebalance partitions.

  Explore platform features that can help you perform regular maintenance and proactively look for new problems. For example, [SQL Database Advisor](/azure/azure-sql/database/database-advisor-implement-performance-recommendations) in Azure SQL Database monitors for problems.

- **Design for automation.** Automated operations are essential for a SaaS solution to scale effectively. Identify regular and occasional tasks and create playbooks or automation scripts for them. For tasks that you can't automate immediately, thoroughly document the processes to ensure consistency and clarity.

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Strive for consistency between customers' data stores when possible.  <br><br>If a customer requires special accommodations, integrate them into an overall process rather than customizing the configuration for that customer. For example, use the same schema for each database, and use the same processes to deploy and manage your resources.|Consistency makes it easier to make changes at scale and minimizes the risk of accidental problems during deployments or maintenance procedures.|
|Deploy limited resources carefully and seek opportunities to streamline operations.|You can avoid small efficiencies and have better resource utilization and overall performance.|
|Build automation for repetitive tasks. Choose to buy automated tools instead of building a custom solution. <br><br> Explore the options that the platform and non-Microsoft vendors provide.| By investing in quality automation, you can repeatedly use these assets and reduce manual tasks that are often prone to errors. Automated tools are valuable if you're not an expert in the data store that you're using or if you're unsure about the necessary maintenance tasks.|
|Deploy your team's database administration capacity carefully. Reserve human database administrators for the most impactful activities, like dealing with large customers or building automation that can scale across many customers.| By prioritizing valuable functions, you can maximize efficiency.|

## Customer access to data

Some of your customers might request direct access to their data for custom reporting or analytics. Carefully consider how customers can access data in your solution and whether to grant these requests or provide alternative methods to meet their needs.

### Design considerations

- **Justify reasons for direct access.** Understand why customers need access to raw data by getting information about their business problem. Collaborate to find a solution that meets their needs without introducing risks to your platform.

  Find alternate ways to meet the requirements rather than giving direct access. If access is needed for reporting purposes, application-tier approaches are preferable. For example, you might build reports for them by using Microsoft Power BI, or you can export a subset of your data to a file that you provide to them. You can also create APIs that they can use to access your data.

- **Evaluate security and isolation implications.** Providing direct access to a data store poses significant security risks. Avoid exposing internal resources to external parties, including customers. In a SaaS environment that has many customers sharing a solution, the risks are even higher because the environment can be exploited to access other customers' data.

  Consider providing customers access to their data in a secure, isolated manner that doesn't affect your production system and lets you make internal database design changes without breaking customers' queries.

- **Consider the effect on performance.** Allowing direct access to your transactional data store can lead to performance issues for your main application. For instance, a customer might run a resource-intensive query that disrupts the application's functionality.

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Avoid giving direct access to your data stores.<br><br>If you must give direct access, provide access to a read-only replica, if the data platform supports it.|Application-tier approaches give you control over how customers use your data. If it's not possible to create application-tier constructs, access through read-only replicas reduces the strain of the customer's queries on your operations.|
|Avoid exposing internal implementation details.|By controlling access to your data structures, you prevent customers from making assumptions about the functionality of your database schema. This flexibility allows you to evolve and optimize your database structure over time without the constraints of customer-built tooling or inaccurate assumptions.|

## Additional resources

Multitenancy is a core business methodology for designing SaaS workloads. These articles provide more information about data design considerations:

- [Architectural approaches for a multitenant solution](/azure/architecture/guide/multitenant/approaches/overview)
- [Architectural approaches for storage and data in multitenant solutions](/azure/architecture/guide/multitenant/approaches/storage-data)
- [Architectural approaches for tenant integration and data access](/azure/architecture/guide/multitenant/approaches/integration)
- [Tenancy models](/azure/architecture/guide/multitenant/considerations/tenancy-models)

## Next step

Learn about DevOps considerations for SaaS workloads, including efficient customer lifecycle management and safe deployment practices.

> [!div class="nextstepaction"]
> [Design area: DevOps practices for SaaS workloads on Azure](./devops.md)
