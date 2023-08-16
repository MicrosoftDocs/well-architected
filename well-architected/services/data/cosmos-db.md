---
title: Azure WAF review for Azure Cosmos DB for NoSQL
description: Design considerations and recommendations for each pillar as related to solutions using Azure Cosmos DB for NoSQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: conceptual
ms.date: 08/16/2023
products:
  - azure-cosmos-db
---

# Azure Well-Architected Framework (WAF) review – Azure Cosmos DB for NoSQL

This article describes the best practices for Azure Cosmos DB for NoSQL. These best practices ensure that you can deploy solutions on Azure Cosmos DB that are efficient, reliable, secure, optimized for cost, and operationally excellent.

## Prerequisites

- [Take the Azure WAF Review assessment](/assessments/?id=azure-architecture-review&mode=pre-assessment)

- Review existing reference architectures:

  - [Globally distributed mission-critical applications using Azure Cosmos DB](/azure/architecture/solution-ideas/articles/globally-distributed-mission-critical-applications-using-cosmos-db)

  - [Serverless apps using Azure Cosmos DB](/azure/architecture/solution-ideas/articles/serverless-apps-using-cosmos-db)

  - [Multi-region web app with Azure Cosmos DB replication](/azure/architecture/solution-ideas/articles/multi-region-web-app-cosmos-db-replication)

## Reliability

TODO

### Design checklist

- Review the behavior of the SDK and SDK configuration options. How so? What to look for? Why?

- Compare and contrast the benefits of multi-region vs single-region write strategies for your workload.

- Ensure your account spans at least two regions in Azure.

- Distribute your account across multiple availability zones when available within your Azure region

- Enable [service-managed failover](/azure/cosmos-db/high-availability#availability) for your account.

- Test the end-to-end high availability of your application. Good one!

- Review the relationship between the [consistency level](/azure/cosmos-db/consistency-levels) and data durability in the presence of a region-wide outage. How so? What to look for? Why?

- Review [common backup processes](/azure/cosmos-db/continuous-backup-restore-introduction) including, but not limited to; point-in-time restore, recovering from accidental destructive operations, restoring deleted resources, and restoring to another region at a point-in-time.

- Assess the [analytical store capabilities](/azure/cosmos-db/analytical-store-introduction) of Azure Cosmos DB. This isn't a reliability concern, but a design matter. I added an article on the next section about it.

- Review network availability considerations. How so? What to look for? Why?

- Explore the [designing resilient applications guide,](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications) review the [default retry policy](/azure/architecture/best-practices/retry-service-specific#azure-cosmos-db) for the SDKs, and plan for [custom handling for specific transient errors](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications#should-my-application-retry-on-errors). Interesting, but needs more context!

Some items above are vague and redundant, we can't tell everybody to use multiple regions, or imply that their architecture isn't good if they don’t use multiple regions. I rather mention something like:

- Cosmo DB has built in high availability with redundancy of data and compute inside a region. Disaster Recovery can be achieved with backups saved in another region. This is cost effective but has high RPO/ RTO. Enabling replication across regions provide the highest level of HA/DR, with near zero RPO/RTO. This can work with [service-managed failover](/azure/cosmos-db/high-availability#availability).

- If your application is globally distributed, you can reduce latency by enabling multi-region writes, allowing the client persist changes to the nearest region.

### Design recommendations

| Recommendation | Benefit |
| --- | --- |
| Distribute your Azure Cosmos DB account across availability zones (when available) | Availability zones provide distinct power, networking, and cooling isolating hardware failures to a subset of your replicas. Using availability zones provides an RTO and RPO of 0. |
| Configure your Azure Cosmos DB account to span at least two regions | Spanning multiple regions prevents your account from being entirely unavailable in the event of an isolated region outage. |
| Enable service-managed failover for your account. | Service-managed failover allows Azure Cosmos DB to change the write region of a multiple-region account to preserve availability. Understand the tradeoffs with service-managed failover and plan for forced failover if necessary. |
| Validate availability by testing failover manually with service-managed failover temporarily disabled | Temporarily disabling service-manage failover allows you to validate the end-to-end high availability of your application with a manual failover started using a script or the Azure portal. Afterwards, you can reenable service-managed failover. |
| Ensure the workload is aligned with document database strengths. | Prevent misuses. Document databases are well suited for high volume CRUD operations of semi structured data with consistently low latency. They're also excellent for single entity lookups with complex/nested attributes. They can also be used for search and analytical workloads as secondary needs, but if these are the primary access pattern for your data consider other engines. |
| Design collections according to application demand.
Don't over normalize, but also keep collection sizes relatively small (a few kilobytes), remove low frequency large attributes to other collections.
Choose a partition key that is frequently used by queries, distributes your data evenly, is non updatable and doesn't exceed the 10-GB limitation.
Remove indexes on attributes not used on searches. | Help improve performance and reduce cost. |
| Analytical workloads consume many resources. If you run frequent aggregations (or joins) over large collections consider enabling the analytical store and doing queries in Synapse Serverless SQL Pools. | Assume you need to calculate the total sales for a month, you need to open all documents in all partitions (fetching unnecessary attributes into memory), then extract the values from json and add them up. Conversely, the analytical stores fields separately in a highly compressed columnar format, so the aggregation would be more efficient. |

### Azure Policy definitions

- [Policy validate at least two regions](https://github.com/Azure/Community-Policy/blob/main/policyDefinitions/Cosmos%20DB/audit-geo-replication-for-azure-cosmos-db/azurepolicy.json)
- [Policy validate service-managed failover](https://github.com/Azure/Community-Policy/blob/main/policyDefinitions/Cosmos%20DB/audit-automatic-failover-for-azure-cosmos-db/azurepolicy.json)

## Security

TODO

### Design checklist

- Implement peripheral (network-level) [security baseline](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#network-security) requirements. What does this mean? How about something like “To reduce attack surface area disable the Public Endpoint of your Cosmos DB cluster and leverage Private Endpoints to inject Cosmos DB into your local virtual network.”

- Implement control plane security baseline requirements related to [identity management](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#identity-management) and [least-privilege access](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#privileged-access).

- Implement [data protection](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#data-protection) security baseline requirements for the data plane.

- Assess service-level [compliance](/azure/cosmos-db/compliance) and [certifications](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) in the context of current global personal data requirements.

- [Encrypt data](/azure/cosmos-db/database-encryption-at-rest) at-rest or in-motion using service-managed keys or customer-managed keys (CMKs).

- Audit user access, security breaches, and resource operations with [control plane logs.](/azure/cosmos-db/audit-control-plane-logs)

- Monitor data egress, data changes, usage, and latency with [data plane metrics](/azure/cosmos-db/use-metrics).

- Secure access to data using [primary/secondary keys](/azure/cosmos-db/secure-access-to-data?tabs=using-primary-key#primary-keys) or [role-based access control](/azure/cosmos-db/secure-access-to-data?tabs=using-primary-key#rbac).

- [Rotate primary and secondary keys](/azure/cosmos-db/secure-access-to-data?tabs=using-primary-key#key-rotation) when they're used.

- Give preference to service principal accounts to connect pass services to Cosmos DB since these accounts don't require passwords.

- If you must authenticate using passwords ~~the keys in the~~ store them in Azure KeyVault

- Data Plane vs Control Plane???

- Data Masking and Redaction???

### Design recommendations

| Recommendation | Benefit |
| --- | --- |
| Implement, at a minimum, the security baseline | Go through the [security baseline](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline) and implement the recommendations to secure your Azure Cosmos DB account. |
| Create virtual network endpoints and rules to limit access to the account. | Implement virtual network service endpoints and firewall rules to restrict access to your Cosmos DB account. Use network security groups (NSGs) to control inbound and outbound traffic to and from the Cosmos DB resources. Limiting access to tRU/sted networks and applying appropriate network security measures helps protect your data from unauthorized access. |
| Lean on role-based access control |  |
| Implement Least Privilege Access Control | Implement least privilege access control by assigning appropriate roles and permissions to users or applications accessing Cosmos DB. |
| Follow best software development practices for secure access to data. | Follow secure coding practices and perform secure code reviews when developing applications that interact with Cosmos DB. Protect against common security vulnerabilities like injection attacks, cross-site scripting (XSS), or insecure direct object references (IDOR). Implement input validation, [parameterized queries](/azure/cosmos-db/nosql/query/parameterized-queries), and appropriate error handling for common [HTTP status codes](/en-us/rest/api/cosmos-db/http-status-codes-for-cosmosdb) to prevent security risks. |
| Security Compliance | Monitoring helps you track access patterns and audit logs, ensuring that your database remains secure and compliant with relevant data protection regulations. |

### Azure Policy definitions

TODO

## Cost optimization

What are the main cost drivers for Cosmos DB?

- Partitioning strategy – Cross partition query drives up the cost if you don’t use the key in your query. Without this more RU/s will be spent.

- Consistency level – Higher consistency leads to higher RU/s that increased to higher costs. This impacts latency.

- Multi write setup drives up the cost.

- Number of replicas there are storage costs. One set of clusters per region.

- Nature of container. If it’s write heavy, (turn off index) -\> Turn off automatic indexing for infrequently used indexes.

- Cost changes in container vs database provisioning. Using DB provisioning isn’t recommended for production. In lower environment, DB might be ok. Consider serverless mode. You can host Cosmos DB in Docker container. This would be free.

- Data modeling (/azure/cosmos-db/nosql/modeling-data)

- Document size. Larger documents accrue higher costs.

- Complexity due to multiple relations. Flattening reduces RU/s.

- Multiple document approaches should be minimized.

- Right-size your collections based on your API usage. (this impacts a document size vs multiple document approach) Strike a balance with normalization.

- Avoid running frequent analytical queries.

- TTL – set some limits based on use.

### Design checklist

- Monitor your RU/s at the very beginning

- Index Policy adjustment (see performance efficiency)

- Design containers with the appropriate partition key (</azure/cosmos-db/partitioning-overview#choose-partitionkey>)

  - Ensure the partition key is a property that has a value that doesn't change.

  - You can't change a partition key definition after container creation.

  - Ensure the partition key has a high cardinality.

  - Ensure the partition key spreads RU consumption and data storage evenly across all logical partitions.

  - Minimize cross-partition queries to reduce RU consumption and latency.

- Consider serverless or shared database throughput for lower environments (dev/test) to minimize RU consumption.

  - </azure/cosmos-db/serverless#use-cases>

  - </azure/cosmos-db/set-throughput#set-throughput-on-a-database>

- Understand your traffic pattern to pick the right option for [provisioned throughput types](/azure/cosmos-db/how-to-choose-offer).

- Understand your workload and how it translates to RU

- autoscale vs Provisioned Throughput

- Know your consistency options – but mention consistency lightly

  - Higher consistency and strong consistency comes with higher costs because of Reads associated

- Data Storage: The amount of data stored in Azure Cosmos DB affects your costs. The total size of your data, including the size of documents, indexes, and metadata, contributes to

- Partitioning is super important because if you search other than the partitioning your queries are going to be super expensive and you're going to spend a ton of money.

  - Choose a partition key that evenly distributes data and load across partitions. Avoid “hot” partitions that receive a disproportionate amount of traffic, as this can lead to higher RU consumption and costs.

- storage costs. Consider data modeling techniques, compression, and indexing strategies to minimize storage

- Scope indexing to only required fields for queries and operations.

- Azure Cosmos DB Autopilot/ autoscale - Azure Cosmos DB Autopilot provides a cost-effective approach by automatically managing throughput based on your workload needs. It ensures that you're only billed for the amount of throughput consumed by your application.

- Multi-write replication – but it can increase costs due to increased data synchronization across region.

- More regions, more replicas mean more costs

- Provisioned Container vs Database provisioned RU/s

- In lower environments such as Dev and Test what are some of the ways we can optimize costs?

  - Use serverless mode for Dev and Test and use provisioned throughput.

  - Use Docker Container to host Cosmos DB and save costs

- Watch your document size - Data Size and Document Design: Keep documents reasonably sized and avoid storing unnecessary data within documents. Smaller documents can lead to better performance, especially in queries and replication.

- Data modeling

  - </azure/cosmos-db/nosql/modeling-data>

- For collections with high volume of reads, partition by an attribute frequently used in searches, this leads to the search getting executed only on the node, which contains the partition instead of all nodes.

- For collections with high volume of writes, turn off automatic indexing for the columns not used in searches.

- Watch your document sizes. Larger documents cost more to read and write. Consider moving your low frequency/large attributes to separate collections.

### Design recommendations

| Recommendation | Benefit |
| --- | --- |
| Choose Appropriate Throughput | Provision the right amount of throughput (Request Units per second, or RU/s) based on your actual workload needs. Start with a lower throughput and scale up as necessary to avoid overprovisioning and unnecessary costs. |
| Use Serverless autoscale | Azure Cosmos DB offers serverless and autoscale options that automatically adjust throughput based on demand. Use these options to optimize costs by avoiding overprovisioning during periods of low traffic. |
| Selective Indexing | Carefully choose which properties to index based on your query patterns. Unnecessary indexes consume RU/s and increase costs. Consider the balance between query performance and RU consumption. |
| Batch Operations | Use batch operations for inserting, updating, or deleting multiple documents in a single request. This reduces the number of individual requests and can lead to better RU efficiency. |
| Query Efficiency | Optimize queries to use appropriate filtering and projection to retrieve only the data you need. Avoid unnecessary scans of large data sets by crafting efficient queries. |
| TTL (Time to Live) | Use TTL to automatically delete data that's no longer needed. This can help manage storage costs by removing expired or obsolete data. |

### Azure Policy definitions

TODO

## Operational excellence

TODO

### Design checklist

- Log SDK diagnostics for outstanding scenarios, such as exceptions or when requests go beyond an expected latency (</azure/cosmos-db/nosql/best-practice-dotnet#capture-diagnostics>).

- Connectivity/availability issues can happen due to lack of resources on your client machine. Monitor your CPU utilization on nodes running the Azure Cosmos DB client, and scale up/out if usage is high ( </azure/cosmos-db/nosql/troubleshoot-service-unavailable>).

- Always using the latest version of the Azure Cosmos DB SDK available for optimal performance and resiliency.

  - Review the minimum recommended version: </azure/cosmos-db/nosql/sdk-java-v4#recommended-version> / </azure/cosmos-db/nosql/sdk-dotnet-v3#recommended-version>

- Consider defining ApplicationName to be included in userAgent suffix, to help analyze requests originating from different applications that use Cosmos DB (</en-us/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationname?view=azure-dotnet> )

- Leverage SDK support for transaction and bulk operations where appropriate

- Monitoring and Alerts

  - Setup proper monitoring and alerting to get notified about performance issues, potential throttling, and other critical events in your Cosmos DB account

  - Use metrics to monitor provisioned throughput usage

    - Analysis of container/database scale in relation to utilization

    - Analysis of manual vs autoscale provisioned throughput

    - Analysis of throughput fragmentation and need to merge or copy containers

- Identify and troubleshoot query performance

- Identify and troubleshoot request timeouts

- Identify and troubleshoot throttled or request rate too large requests

- Identify and troubleshoot item not found

- Identify and troubleshoot unauthorized or forbidden requests

- Identify and troubleshoot bad or failed requests

- Identify potential availability issues in multiregional environments

- Monitoring for older and deprecated SDK version usage

- Monitoring of throttling to identify

- Execute regular key rotation: </azure/cosmos-db/secure-access-to-data?tabs=using-primary-key#key-rotation>Execute regular key rotation (every X months): /azure/cosmos-db/secure-access-to-data?tabs=using-primary-key#key-rotation

  - Monitoring for regular key rotation (</azure/cosmos-db/monitor-account-key-updates>)

- Monitoring for request failures and request unexpected latency (this can be server side or client side, for client side the first bullet covers it).

- Automate Deployments to reduce the chance of human errors

- Metrics: RU utilization (simple metric to see if overprovisioned or not), RU utilization by partition (to spot hot partition issue), throttling (overall app health), request volumes by type (do the server-side reported metrics align with expectation, are there spikes?)

### Design recommendations

| Recommendation | Benefit |
| --- | --- |
| Monitor RU/s Throttling | Monitoring allows you to track the Request Units consumption, query performance, and throughput patterns and inefficient queries |
| Throttling Detection | Cosmos DB employs a request unit-based pricing model and exceeding the provisioned RU/s can lead to throttling. Monitoring helps you detect and address potential throttling issues, preventing downtime or performance degradation due to resource limiations |
| Normalized RU/s | [Monitor normalized RU/s for an Azure Cosmos DB container or an account | Microsoft Learn](/azure/cosmos-db/monitor-normalized-request-units) |
| Server Side Latency | [How to monitor the server-side latency for operations in Azure Cosmos DB | Microsoft Learn](/azure/cosmos-db/monitor-server-side-latency) |
| Operational Insights | Monitoring provides valuable insights into the behavior of your |
|  |  |

### Azure Policy definitions

TODO

## Performance efficiency

- Partition

- key

- Monitoring the utilization of partition.

- Baseline – Number of transaction, number of users. Throughput. Average operations (User flows). Understand the peaks.

- Test – End to end latency.

- Categories for optimization:

- Lookups

- Joins

- Queries

- Aggregates

Client side optimization (prefetching)

Server side optimization Memory integrated cache. You can’t really control it.

### Design checklist

- Come up a Performance Baseline

  - How many concurrent users and transactions you will need to handle

  - Setting up your throughput

- Avoid Hotspots

- Deploy Azure Cosmos DB and the application in region(s) that are close to end users

- Specify application preferred regions ([/azure/cosmos-db/nosql/best-practice-java /](/azure/cosmos-db/nosql/best-practice-java%20/) </azure/cosmos-db/nosql/best-practice-dotnet>)

- Use Direct mode for the best performance. ()</azure/cosmos-db/nosql/sdk-connection-modes>)

  - Direct mode is the preferred option for best performance – it allows your client to open TCP connections directly to partitions in the Azure Comos DB back-end and send requests directly with no intermediary

  - Offers better performance because there are fewer network hops

- Customize index policy based on application requirements

  - “Exclude all, except” is a good pattern (</azure/cosmos-db/nosql/performance-tips-dotnet-sdk-v3?tabs=trace-net-core#indexing-policy>)

  - Game plan here is to reduce RU/s

  - Exclude unused paths from indexing for faster writes

- Bulk Inserts – Turning off Indexing for Bulk Insert Operations can help with speed

- Composite Indexes

  - If you want even better performance for queries that have Order By’s Composite indexes are a great fit

  - With a smart composite index you can get an order of magnitude more performance

- See partition key design on cost optimization

- For most common cases of production workloads, we highly recommend using at least 4-cores and 8-GB memory VMs whenever possible. Host should be 64-bit operating system. ([Azure Cosmos DB best practices for Java SDK v4 | Microsoft Learn](/azure/cosmos-db/nosql/best-practice-java) / </azure/cosmos-db/nosql/best-practice-dotnet#checklist> )

- To reduce latency and CPU jitter, enable accelerated networking on client virtual machines in both Windows and Linux. ([Use PowerShell to create a VM with Accelerated Networking | Microsoft Learn](/azure/virtual-network/create-vm-accelerated-networking-powershell))

- Use a single instance of CosmosClient for the lifetime of your application for better performance (singleton).

- Document Size matters

  - Larger documents consume higher RU/s for reads and writes, so try to keep your documents small for best performance. Try and keep documents less than 2 MB in size. Maybe split data across items if I'm greater than that 2 MB limit.

- Collections/Containers

  - If a particular collection is seeing spikes in throughput, you can manage its throughput level in isolation by increasing or decreasing the value. This change to the throughput level of a particular collection will not cause side effects for the other collections. This allows you to adjust to meet the performance needs of any workload in isolation.

  - If you want guaranteed performance for the container provision Throughput at the Container Level

- Optimize Queries

  - You want to help your query by ensuring it will be routed to the proper and expected partitions so they have optimal performance

    - Make sure you use the Cross Partition ID in the query itself for better performance

  - Sub-Queries will make you a hero in Cosmos DB

    - Subqueries make it a lot easier to work with arrays in Cosmos DB

    - Subqueries let you filter before joining and that can improve the speed of your query.

- TTL (Time to Live) - Think about using the Time to Live to remove data that isn't required.

  - Less Data is going to op

- When possible, keep documents small. Also, separate frequently changing documents from slow-changing documents.

- What are the top or most important metrics customers should be aware of and what are appropriate levels or measures or percentages or limits etc.….

- **Design collections according to application demand.**
  
  - **Don't over normalize, but also keep collection sizes relatively small (a few kilobytes), remove low frequency large attributes to other collections.**

### Design recommendations

| Recommendation | Benefit |
| --- | --- |
| TODO | |

### Azure Policy definitions

TODO

## Tradeoffs

- Consistency Levels: Cosmos DB offers five levels of consistency: Strong, Bounded staleness, Session, Consistent Prefix, and Eventual. Choosing a higher consistency level ensures stronger data consistency but might impact performance due to increased latency and potential conflicts. Lower consistency levels provide better performance but might lead to eventual consistency and possible conflicts.

- Latency vs. Consistency: The more consistent you want your data to be across different regions, the higher the latency might become due to synchronization and replication. Balancing the tradeoff between low-latency access and strong consistency is crucial for your application's requirements.

- Throughput vs. Cost: Provisioned throughput is a critical factor in Cosmos DB's pricing model. While higher throughput ensures better performance, it also increases costs. Finding the right balance between throughput and cost can be a significant tradeoff.

- Partitioning Strategy: Cosmos DB uses partitioning to scale out horizontally. Choosing the right partitioning key is crucial for performance. If not chosen correctly, it can lead to "hot" partitions that receive a disproportionately high amount of traffic, causing performance bottlenecks.

- Global Distribution: While Cosmos DB provides global distribution for high availability and low-latency access, replicating data across regions can increase costs and complicate data synchronization and management.

- Query Performance: Complex queries and large data sets can impact query performance. Depending on your application's requirements, you might need to denormalize data or use appropriate indexing strategies to optimize query performance.

- Cost vs. Features: Cosmos DB offers various features such as automatic indexing, backups, and multi-region replication, but enabling these features might increase costs. Balancing the feature set against your budget is an important tradeoff.

- Measure effectiveness of partitioning strategy (for example, inspect Normalized RU throughput metric).

- Limit number of concurrent updates to the same document.

- Limit or eliminate cross-partition queries.

## Extra resources

TODO

### Azure Architecture Center guidance

- [TODO](about:blank)

### Cloud Adoption Framework guidance

- [TODO](about:blank)

## Next steps

- [Deploy an Azure Cosmos DB account](/azure/cosmos-db/nosql/quickstart-portal)
