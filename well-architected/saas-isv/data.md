---
title: Data for SaaS workloads on Azure
description: Learn about the data platform considerations for SaaS workloads.
author: johndowns
ms.author: jodowns
ms.date: 09/16/2024
ms.topic: conceptual
---

# Data for SaaS workloads on Azure

[!INCLUDE [header_file](includes/temporary-warning.md)]

Treat data as the most valuable asset of your solution. As Independent Software Vendor (ISV), be aware that you're responsible for managing your customers' data. Your data design strategy and choice of data store can have significant impact on your customer.

This article offers considerations to help you keep the promise of data integrity and confidentiality to your customers, while delivering performance as defined by the business requirements.

## Select a data store

The choice of data store in a SaaS solution significantly impacts its architecture, performance, reliability, and transactional complexity. Compare the capabilities of Azure's managed services with similar third-party offerings. In some situations you might also compare with running open source products on virtual machines.

### Design considerations

- **Differentiate between your transactional and analytical operations.** Transactional and analytics data stores have distinct requirements. Transactional data stores support your applications, while analytics data stores are used for reporting and purposes like machine learning. These stores are built with specialized products and have unique needs for performance, access patterns, schemas, and use cases.

	This guide focuses on transactional data stores.

- **Understand your data needs.** Estimate the volume, frequency at which it can change, and type of data you'll store.

	Expect data to grow significantly over time. For SaaS solutions, growth occurs in multiple dimensions. Anticipated increase in volume and type of data, as the number of customers grow. Make sure you plan for that growth and invest in technologies that can support it.

	Decide between a relational or non-relational data platform based on the nature of your data. For many transactional workloads, a relational database is a good option for modeling application entities as discrete tables, allowing queries to operate across the relational data model. Alternatively, if your data naturally fits a document model or follows a graph structure, a non-relational approach may be more suitable.
 
	For more information, see [Relational or non-relational data platform](/dotnet/architecture/cloud-native/relational-vs-nosql-data).

- **Minimize the types of data stores.** The approach of storing different types of data in multiple distinct data stores can be beneficial for mature organizations with expertise across various data platforms. However, it often introduces unnecessary complexity for startups and smaller organizations. It's usually more effective to focus on one or a small number of data stores.
  
	If you don't have the business justification for adopting that approach, avoid it. Instead, focus your efforts on one or a small number of data stores.

- **Use what you know, and invest in it.** If your team already has expertise with a specific data store, it's usually better to use that instead of investing in learning new skills. Data stores and platforms are complex, and design decisions can be difficult to reverse.

	However, keep in mind the potential growth. If your current data store no longer meets your requirement, choose a data store that can enhance your solution's performance, resiliency, security, and operational efficiency but also allows your team to deepen their expertise. 

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Separate transactional data stores, for day to day operations, from analytics and reporting data stores.|Mixing the intent of your data store can lead to unnecessary complexity. Segmentation will lead to better operations and utilization of each data store. |
|Choose between relational or non-relational data structure based your requirements. Start with one, or a small number, of data store. <br><br>Prioritize managed data stores. Common choices include Azure Cosmos DB, Azure SQL, MySQL, MongoDB, and PostgreSQL.|This approach helps minimize complexity and ensures you use the right product to maximize efficiency. Managed data stores offer flexibility in managing resources and costs elastically, scaling with your needs. This reduces the management burden compared to deploying your own data store on your own infrastructure.|
|Invest in learning your chosen technology. Equip your team to manage the high scaling requirements and other complexities of SaaS solutions.| You'll benefit from learning about the tools you use and their wider ecosystem, so that you can make effective use of your data platform as you scale.|
|Adopt flexiblity in your data design.|As your SaaS solution grows or your requirements change, you can adapt by adding or changing data stores. This flexibility allows you to start with one data store and evolve over time to meet your needs.|

## Tenancy model and database strategy

A key aspect of data design is the decision to host resources on behalf of your customers or host resources in their environment. Most SaaS providers host resources for all customers, offering flexibility in database management. If hosting in the customer's environment, consider how you'll access and manage those resources.

### Design considerations

- **Plan your database segmentation.** In B2B SaaS solutions, creating dedicated databases for each customer is advisable. This approach enhances data security by maintaining strict isolation between customers, reducing the risk of data mixing, and supporting customer-managed encryption keys. It also helps meet regulatory compliance requirements for some customers.

	Separating customer data into individual databases can improve performance by minimizing [noisy neighbor issues](/azure/architecture/antipatterns/noisy-neighbor/noisy-neighbor). Some managed data stores offer resource allocation controls to mitigate these issues, provide cost efficiencies, and include tools for managing multiple databases at scale.

	In some cases, it's appropriate to store multiple customers' data in a single data store. For example, in B2C solutions, data can be saved in one store with logical partitioning by customer ID. In B2B solutions with shared components, a single data store can be used for specific parts, such as an event store, while ensuring customer IDs are included on each event.

- **Collocate data stores with application components.** If hosting on behalf of a customer, deploy in the same Azure region to avoid egress bandwidth charges and latency. When hosted in a customer's environment, deploy both the application and data store together in the same environment to avoid cross-environment complexities.
  
- **Standardize data store management, as much as practical.** Uniformity is key to managing data across customers. As your business grows, differences between customers increase risk and complexity, making production outages more likely and troubleshooting more difficult.

  Avoid one-off changes in your management to support individual customers. For example, to support customer-managed metadata, avoid schema changes like adding extra columns to your database. Instead, build functionality for customers to add their own metadata. Similarly, if you need to provide different levels of database performance for different customers, create a single process that you can use to apply different configuration to different tiers of customer.
  
To learn more about how your tenancy model affects your data strategy, see [Architectural approaches for storage and data in multitenant solutions](/azure/architecture/guide/multitenant/approaches/storage-data).

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Evaluate whether to share databases between multiple customers or provide a shared data platform. <br><br> Deploy a single database for each customer's data, where appropriate. Relax this strategy if strict isolation isn't a requirement, such as in B2C solutions.| This approach minimizes noisy neighbor issues, and can support compliance requirements for some customers.|  
|Deploy applications and their databases in the same region.|You'll optimize on bandwidth cost and latency incurred by cross-region database access.|
|Design a standardized approach for storing customer-defined data or metadata. Avoid altering the schema for individual customers or causing customer environments to differ.|You'll be able to avoid operational burden of managing inconsistencies between database for each customer.|
|Plan for routine maintenance operations in the customer-deployed environment. <br><br>Plan how the database can be accessed for updates, schema changes, maintenance, and other operations.|This proactive approach minimizes issues from lack of maintenance, reducing the risk of downtime and performance problems.|


## Provision capacity

Capacity planning involves managing resource utilization, focusing on CPU, memory, storage, and disk operations (IOPS). Some data stores combine these resources into a simpler synthetic resource consumption metric, like a database throughput unit (DTU) in Azure SQL or a request unit (RU) in Azure Cosmos DB. Managed data stores offer flexibility in resource management, allowing changes over time. It's crucial to establish an initial plan and iterate as needs evolve.

### Design considerations

- **Understand your resource allocation requirements.** Different customers in SaaS solutions may have varying resource needs. Smaller customers might require minimal resources, while larger customers may need more. Larger customers often pay more, justifying higher resource allocation. Using separate databases for each customer allows you to tailor resource allocation based on their size and needs.

- **Understand the different capacity models offered by data platforms.** Cloud-based data platforms often provide multiple resource models. For example, some Azure services like Azure Cosmos DB provide provisioned throughput-based models and serverless models. Azure SQL Database also provides elastic pools.

	Provisioned throughput requires predetermined resource allocation, either from a single database or a group of databases. [Elastic pools](/azure/azure-sql/database/elastic-pool-overview) allow resource sharing among multiple databases. Elastic pools are commonly used in SaaS solutions.
  
	Even though provisioned throughput requires that you allocate resources ahead of time, services like Azure Cosmos DB offer [autoscale](/azure/cosmos-db/provision-throughput-autoscale). You can set rules for dynamically adding or removing resources based on specified triggers.
	
	Serverless resource models automatically scale based on demand, making them a good starting point if you can't predict your capacity requirements ahead of time. However, they may support fewer features and become cost-inefficient as you grow. Serverless models are available in [Azure SQL Database](/azure/azure-sql/database/serverless-tier-overview) and [Azure Cosmos DB](/azure/cosmos-db/serverless).

### Design recommendations
|Recommendation|Benefit|
|---|---|
|Model your database requirements for each customer. Determine whether you'll have many small databases, fewer large databases, or a mixture of the two. <br><br>Use a t-shirt sizing exercise to categorize customers into small, medium, and large buckets.|This approach provides a rough estimate of resources needed per customer and helps in mapping customers to your billing model.|
|Segment resource pools based on the size of the customer databases that use them.  <br><br> Use provisioned capacity to your advantage. For example, you might create a shared SQL elastic pool for smaller customers, a separate pool for medium customers, and dedicated resources for large customers.|Segmenting resource pools based on customer database size allows for optimized resource allocation and cost efficiency. |
|Take advantage of the built-in scaling capabilities offered by the managed services.|You can offload scaling responsibilities to the platform. Features like elastic pools and autoscale can help optimize resource use.|
|Regularly review serverless data stores to ensure they continue to meet your needs.|You'll be able to ensure that the data store remain effective with your evolving needs, optimizing performance and cost-efficiency as your requirements change.|

## High availability and disaster recovery

Customers of SaaS solutions often have high expectations for high availability (HA) and disaster recovery (DR). If your customers operate in regulated industries or rely on your solution for daily operations, their requirements may be even more stringent.

HA and DR aren't one-size-fits-all solutions and depend on various factors. Have a clear understanding of the available options that are applicable to both your and your customers' requirements to make informed decisions about mitigating different risks.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Resiliency for data services often requires distributing replicas or copies of your data across a wider geographic area to mitigate risks. However, there are tradeoffs. The longer the distance data has to travel, the more protection you have against localized failures. But, copying data across longer distances increases latency and often costs more.
> Many managed data stores offer automated data replication, but they may impose limits on the types of replication you can perform across different distances to maintain performance.

### Design considerations

- **Quantify resiliency.** Resiliency requirements are measured using service level objectives (SLOs), which include metrics like uptime, recovery time, and recovery point. These metrics are driven by both your business requirements and those of your customers, who may have varying needs. If you store large amounts of data on behalf of customers, your HA and DR solution might need to be more complex to meet stringent requirements.

   To learn more about resiliency metrics, see [RE:04 Recommendations for defining reliability targets](../reliability/metrics.md).

- **Use platform features.** Azure offers capabilities for resiliency within a datacenter, within a region using availability zones, and across a wider geographic area using multiple regions. Combine strategies like availability zones, cross-region backup, multi-region deployments to achieve the right level of resiliency for your solution. For stringent requirements, consider a multi-region active-passive architecture with asynchronous data replication between regions. This approach may result in some data loss during a catastrophic outage.

	> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Multi-region active-active designs with replication are the most resilient, but are complex to build and test. For most active-active solutions, you need to design a conflict resolution approach that accounts for delays in data synchronization. Most solutions don't need this degree of resiliency.

	For more information about using availability zones and regions in your solution, see [RE:05 Recommendations for using availability zones and regions](../reliability/regions-availability-zones.md).

- **Use deployment stamps to isolate the blast radius of components.** The Deployment Stamps pattern is widely used in SaaS solutions, offering benefits for deployment, management, performance, and resiliency. For instance, deploying a stamp in the United States and another in Europe ensures that customers in one region are isolated from outages in the other and can operate independently.

   For more information about how deployment stamps work in a SaaS solution, see [Architectural approaches for a multitenant solution](/azure/architecture/guide/multitenant/approaches/overview).

### Design recommendations

|Recommendation|Benefit|
|---|---|
|Focus on resiliency requirements keeping in mind the overall data requirements (yours and your customers').|By grounding your design decisions in requirements, you ensure you make the appropriate tradeoffs and you avoid under- or over-engineering for your needs.| 
|Reflect varying levels of resiliency in your billing model. <br><br> Set expectations with your customers, as 100% uptime or zero data loss during catastrophic outages may be unrealistic.| Billing model can help your customers understand how much resiliency guarantees they're signing up for. For example, with a lower-tier, customers get minimal guarantees. However, in a higher-tier, customers receive more resiliency because you can afford to replicate their resources across multiple regions.|
|Use Azure availability zones for production solutions. Where possible, use zone redundant data stores.|Availability zones provide resiliency against datacenter outages, without significantly increasing the cost, latency, or complexity of your solution.|
|Keep backups of your data stores in a globally redundant format, using cross-region replication where available.|Cross-region backups of data add an extra level of resiliency.|
|Use deployment stamps to create separate instances of your solution in geographically distributed locations.|Using deployment stamps to create separate instances of your solution in geographically distributed locations increasing resiliency and provides additional benefits such as easier operations management.|
|Evaluate if you need multi-region deployment and if an active-active design is needed to meet the requirements. <br><br> Consider the tradeoffs involved. Stateless components are much easier to replicate than stateful components like a data store. Also, |Spreading your solution or stamps across regions provides higher levels of resiliency by replicating data between regions.|

## Security and compliance

You're responsible for ensuring the confidentiality and integrity of your customers' data. As you build a security baseline, consider your security requirements and promises. Plan to meet your customers' compliance needs, including data retention.

### Design considerations

- **Networking.** Consider _who will access your data store_. Typically, only your application needs direct communication, so configure it for private-only networking.

- **Identity.** Consider _how your data stores will be accessed_. Many SaaS solutions use a single application identity for all data stores, with the application tier enforcing isolation and authorization. For row-level security or database-level authorization, you may need to propagate the user's identity to the data store, which is complex in a multitenant environment.

- **Data retention.** Plan your data retention policies in advance. Maintaining more data increases storage costs and management complexity. For instance, large amounts of data in a transactional database can make querying and truncating more difficult.

	For long-term data retention, such as for compliance or future analyses, consider relocating data to a store suitable for long-term retention.

### Design recommendations
|Recommendation|Benefit|
|---|---|
|Configure your data stores to use private endpoints, and disable public endpoints.|This approach enhances security by restricting access to your internal network. This reduces the risk of unauthorized access and potential data breaches.|
|Use managed identities and Microsoft Entra ID for authentication. Avoid the use of database keys or credentials.|Managed identities eliminate the need for database keys or credentials, reducing the risk of credential theft and simplifying access management.|
|When working with shared data stores, ensure the application scopes all requests to a single tenant by including the tenant identifier in `WHERE` clauses.|This helps mitigate the risk of cross-tenant data leakage or impersonation.||
|Plan your data retention strategy based on compliance and business needs. Avoid keeping unnecessary historical data. For long-term retention, move data from primary stores to archival storage.|By avoiding unnecessary retention, you maintain a smaller surface area.|
|Use data store features to support your data lifecycle needs, such setting the [time-to-live (TTL)](/azure/cosmos-db/nosql/time-to-live) on documents in Azure Cosmos DB.|This ensures efficient data lifecycle management, reduces manual intervention, and optimizes storage by automatically archiving or deleting outdated data.|

## Operations

SaaS solutions often include a large number of databases or other stores. It's important to plan for routine maintenance on your fleet, and explore automation options to manage these tasks efficiently.

### Design considerations

- **Understand your team's capabilities.**  If you don't have large teams of database administrators who can perform detailed analyses on individual customers' databases, have a plan to perform operations at scale, utilizing platform tooling whenever possible.

- **Plan your regular maintenance procedures.** List the regular maintenance operations needed and their frequency. The specific operations will vary based on the type of data store used. For example, 
  - Monitoring the total amount of data, and data in specific entities like important tables.
  - Rebuilding indexes.
  - Creating or removing indexes based on changing query patterns.
  - Rebalancing partitions.

  Explore platform features that can help you to perform regular maintenance and to proactively look for new issues. For example, Azure SQL Database, has [Database Advisor](/azure/azure-sql/database/database-advisor-implement-performance-recommendations) that monitors for issues. 

- **Design for automation.** Automated operations are essential for a SaaS solution to scale effectively. Identify regular and occasional tasks, and create playbooks or automation scripts for them. For tasks that can't be automated immediately, thoroughly document the processes to ensure consistency and clarity.

### Design recommendations
|Recommendation|Benefit|
|---|---|
|Strive for consistency wherever possible between customers' data stores.  <br><br>If special accommodations are needed for a customer, integrate them into an overall process rather than making one-off changes. For example, use the same schema for each database, and use the same processes to deploy and manage your resources.|Consistency makes it easier to make changes at scale, and minimizes the risk of accidental problems during deployments or maintenance procedures.|
|Deploy limited resources carefully and seek opportunities to streamline operations.|You'll avoid small efficiencies and have better resource utilization and overall performance.|
|Build automation for repetitive tasks. Prefer buying automated tools instead of build a custom solution. <br><br> Explore options provided by the platform and third-party vendors.| Investing in quality automation ensures you can repeatedly use these assets and reduce manual tasks that are often prone to errors. Automated tools are particularly valuable if you're not an expert in the data store you're using or are unsure about the necessary maintenance tasks.|
|Deploy your team's database administration capacity judiciously. Reserve human database administrators for the most impactful activities, like dealing with large customers or building automation that can scale across many customers.| By prioritizing valuable functions, you maximize your efficiency.|

## Customer access to data

Some of your customers may request direct access to their data for custom reporting or analytics. Carefully consider how customers can access data in your solution and whether to grant these requests or provide alternative methods to meet their needs.

### Design considerations

- **Justify reasons for direct access**. Understand why customers need access to raw data by getting information about their business problem. Collaborate to find a solution that meets their needs without introducing risks to your platform.

	Find alternate ways to meet the requirements rather than giving direct access. If access is needed for reporting purposes, application-tier approaches are preferable. For example, you might build reports for them by using Microsoft Power BI, you could export a subset of your data to a file that you provide to them, or you could create APIs that they can use to access your data. For more information, see [Architectural approaches for tenant integration and data access](/azure/architecture/guide/multitenant/approaches/integration).

- **Evaluate security and isolation implications.** Providing direct access to a data store poses significant security risks. Avoid exposing internal resources to external parties, including customers. In a SaaS environment with many customers sharing a solution, the risks are even higher, as it could be exploited to access other customers' data.

	Consider providing customers access to their data in a secure, isolated manner that avoids impacting your production system and allows for you to make internal database design changes without breaking customers' queries.

- **Consider the performance impact.** Allowing direct access to your transactional data store can lead to performance issues for your main application. For instance, a customer might run a resource-intensive query that disrupts the application's functionality.


### Design recommendations

|Recommendation|Benefit|
|---|---|
|Avoid giving direct access to your data stores.<br><br>If you must give direct access, provide access to a read-only replica, if supported by the data platform.|Application-tier approaches give you control over how customers use your data. If it's not possible to create application-tier constructs, access through read-only replicas reduces the direct impact of the customer's queries on your operations.|
|Avoid exposing internal implementation details.|By controlling access to your data structures, you prevent customers from making assumptions about the functionality of your database schema. This flexibility allows you to evolve and optimize your database structure over time without being constrained by customer-built tooling or inaccurate assumptions.|
