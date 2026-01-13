---
title: Architecture Best Practices for Azure Cosmos DB for NoSQL
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Cosmos DB for NoSQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: concept-article
ms.subservice: waf-service-guide
ms.date: 11/21/2025
products:
  - azure-cosmos-db
---

# Architecture best practices for Azure Cosmos DB for NoSQL

Azure Cosmos DB is a fully managed database solution that can host multiple database types like NoSQL, relational, vector, and table databases. It can accommodate most use cases for your workload. The recommendations in this guide focus on the [Azure Cosmos DB for NoSQL](/azure/cosmos-db/nosql/) variant. You can also apply some foundational recommendations to other variants.

This article assumes that as an architect, you've reviewed the [Azure data options](/azure/architecture/guide/technology-choices/data-options) and chose Azure Cosmos DB for NoSQL as the data store for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Cosmos DB for NoSQL

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](../reliability/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the consistency levels, cross-region replication, and provisioned throughput. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Choose a simple design and avoid unnecessary features.** Don't adopt functionality that unnecessarily adds complexity to your workload, like multiple-primary writes and custom indexing. Azure Cosmos DB supports these features, but they can add significant complexity and operational burden. Carefully evaluate whether your workload requires any advanced features.
>
>    Offload workload responsibilities and cross-cutting concerns to other services, such as Microsoft Entra ID for authentication and authorization or Azure Monitor and Application Insights for monitoring.
>
> - **Identify and prioritize workload flows.** Not all flows in the application are equally important. Identify the critical paths and assign priorities to each flow to guide your design decisions. User flow design can influence your service tiers, features, and the capacity that you allocate to the Azure Cosmos DB database.
>
>   To help identify critical flows that might need special treatment, capture the processing requirements that set them apart in terms of data processing capabilities. For example, some flows might require higher throughput or be more sensitive to latency problems. And some solution flows might require different transaction consistency levels.
>
> - **Use failure mode analysis to identify potential failures in your workload.** Plan mitigation strategies for potential failures. The following table shows examples of failure mode analysis.
>
>   |Failure|Mitigation|
>   |---|---|
>   |High latency and timeouts after rollout to users in a new geographical region| Enable multiple-region replication to an Azure region that's closer to the new users.|
>   |Throttling because of exceeding request units (RUs)| Implement retry logic. Monitor RU consumption and adjust throughput settings as needed.|
>   |Azure Cosmos DB service failure in a single zone in a single Azure region| Configure availability zone support when you create a database.|
>
>   For more information, see [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).
>
> - **Always use the SDK to access your databases.** Use the Cosmos DB SDK in your clients to access Cosmos DB, and [tune client side error handling parameters](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) for both reads and writes. The SDK has built-in transient fault handling to minimize custom logic. Where built-in handling isn't sufficient, the SDK supports your custom error handling implementations, such as handling [rate limiting errors](/azure/cosmos-db/performance-tips?tabs=trace-net-core#429). The SDK also allows you to capture rich [diagnostics logs](/azure/cosmos-db/best-practice-dotnet#capture-diagnostics) on the client to provide reliability observability.
>
> - **Build redundancy to improve resiliency and help meet reliability targets.** Design your database account deployment so it spans at least two regions in Azure. Distribute your account across multiple availability zones if your Azure region supports it.
>
>   Evaluate the multiple-region and single-region write strategies for your workload. For single-region write, design your workload to have at least a second read region for failover. Enable automatic failover for single-region write and multiple-region read scenarios. For multiple-region write, compare the tradeoffs in complexity and consistency against the advantages of writing to multiple regions. For more information, see [Expectations during a regional outage for single-region and multiple-region write accounts](/azure/cosmos-db/high-availability#what-to-expect-during-a-region-outage).
>
>   Consider how your [consistency level](/azure/cosmos-db/consistency-levels) and replication mode [affects the recovery point objective (RPO)](/azure/cosmos-db/consistency-levels#rto) in a region-wide outage.
>
>   Design an end-to-end test of high availability for your application that includes testing database failover and fail back.
>
> - **Use native disaster recovery and backup features.** Implement [database and container backups and restores](/azure/cosmos-db/continuous-backup-restore-introduction) to meet your requirements. Restore scenarios include point-in-time restore, recovering from accidental destructive operations, restoring deleted resources, and restoring to another region at a point in time. Configure your account with [continuous backup](/azure/cosmos-db/online-backup-and-restore). Choose the appropriate retention period based on your business requirements.
>
> - **Align your Azure Cosmos DB design with industry-standard application design guidance and patterns.** Explore the [guide to design resilient applications](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications), review the [default retry policy](/azure/architecture/best-practices/retry-service-specific#azure-cosmos-db) for the SDKs, and plan for [custom handling for specific transient errors](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications#should-my-application-retry-on-errors). These guides provide best practices to make application code resilient to transient errors.

### Configuration recommendations

| Recommendation | Benefit |
| --- | --- |
| Distribute your Azure Cosmos DB account across [availability zones](/azure/reliability/reliability-cosmos-db-nosql) when available. | Availability zones provide segregated power, networking, and cooling, which helps isolate hardware failures to a subset of your replicas. When you don't use the availability zones feature, Azure Cosmos DB spans multiple replicas across a single, randomly selected availability zone. |
| Configure your Azure Cosmos DB account to span at least [two regions](/azure/cosmos-db/distribute-data-globally). | Spanning multiple regions helps ensure that your workload is resilient to regional outages. If there is a failure in a  write region, you can read from another replica. You can also fail over the write region to another region. Using the client SDK enables your client to be isolated from the mechanics of these failovers. |
| Enable [service-managed failover](/azure/cosmos-db/how-to-manage-database-account#enable-service-managed-failover-for-your-azure-cosmos-db-account) for your account. Understand the tradeoffs with service-managed failover, and plan for forced failover if necessary.<br><br>Temporarily disable service-managed failover to validate the end-to-end high availability of your application. You can start a manual failover by using a script or the Azure portal.| Service-managed failover allows Azure Cosmos DB to automatically change the write region of a multiple-region account to preserve availability. This change occurs without user interaction.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Cosmos DB for NoSQL.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines.** To enhance the security posture of your workload, review the [security checklist for Azure databases](/azure/security/fundamentals/database-security-checklist) and the [security baseline for Azure Cosmos DB](/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline). At a minimum, implement the [data protection](/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#data-protection) and [identity management](/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#identity-management) security baselines to help secure your Azure Cosmos DB account.
>
>   Assess service-level [compliance](/azure/cosmos-db/compliance) and [certifications](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) in the context of current global personal data requirements.
>
> - **Implement strict, conditional, and auditable identity and access management.** Use Microsoft Entra ID for your workload's authentication and authorization needs. Microsoft Entra ID provides centralized authorization and access management.
>
>   For control plane and data plane access to your account, create roles, groups, and assignments based on the principle of [least-privilege access](/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#privileged-access). Consider [disabling key-based authentication](/azure/cosmos-db/how-to-setup-rbac#disable-local-auth).
>
>
> - **Encrypt data.** [Encrypt data](/azure/cosmos-db/database-encryption-at-rest) at rest or data in motion by using service-managed keys or customer-managed keys.
>
> - **Apply network segmentation and security controls.** Create intentional segmentation and perimeters in your network design and apply defense-in-depth principles by using localized network controls at all network boundaries.
>
>   Reduce the surface attack area by using private endpoints in accordance with the [security baseline](/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#network-security) for Azure Cosmos DB.
>
> - **Implement a holistic security monitoring strategy.** Audit user access, security breaches, and resource operations by using [control plane logs](/azure/cosmos-db/audit-control-plane-logs).
>
>   Monitor data egress, data changes, usage, and latency by using [data plane metrics](/azure/cosmos-db/use-metrics).
>
> - **Use secure development and testing practices.** Follow best software development practices for secure access to data. Follow secure coding practices and perform secure code reviews when you develop applications that interact with Azure Cosmos DB.

### Configuration recommendations

| Recommendation | Benefit |
| --- | --- |
| Disable public endpoints and use [private endpoints](/azure/cosmos-db/how-to-configure-private-endpoints?tabs=arm-bicep) whenever possible. | Using private networking through private endpoints reduces the surface area susceptible to attacks on your account. |
| Use [role-based access control (RBAC)](/azure/cosmos-db/nosql/security/how-to-grant-data-plane-role-based-access) to limit control-plane access to specific identities and groups within well-defined assignments. | Using RBAC helps prevent unauthorized access to your account. Assign appropriate roles and permissions to users or applications that access Azure Cosmos DB based on the principle of least privilege. |
| Create [virtual network](/azure/cosmos-db/how-to-configure-vnet-service-endpoint) endpoints and rules to limit access to the account. Use network security groups (NSGs) to control traffic to and from Azure Cosmos DB resources.| Virtual network service endpoints, NSGs, and firewall rules help restrict access to your Azure Cosmos DB account and help protect your data from unauthorized access. |
| Protect against common security vulnerabilities like injection attacks, cross-site scripting, or insecure direct object references. Implement input validation, [parameterized queries](/azure/cosmos-db/nosql/query/parameterized-queries), and appropriate error handling for common [HTTP status codes](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) to prevent security risks. | Input validation helps prevent malicious data from being processed by your application. This approach blocks potentially harmful data that could lead to security breaches or data corruption.<br><br>Proper error handling enables your application to respond to errors in a controlled manner and prevents the exposure of sensitive information.|
| [Monitor control plane logs](/azure/cosmos-db/audit-control-plane-logs) to detect unauthorized modifications to your Azure Cosmos DB account. | Monitoring helps you track access patterns and audit logs to ensure that your database remains secure and compliant with relevant data protection regulations. Monitoring data-plane metrics can also help identify unfamiliar patterns that might reveal a security breach. |
| Enable [Microsoft Defender for Azure Cosmos DB](/azure/defender-for-cloud/defender-for-databases-enable-cosmos-protections) to trigger security alerts when anomalous activities occur. | Microsoft Defender detects attempts to exploit databases in your account. Defender detects potential SQL injections, suspicious access patterns, and other potential exploitation activities. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Cosmos DB for No SQL and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Optimize your Azure Cosmos DB scaling strategy.** Understand how Azure Cosmos DB handles [storage and throughput scaling](/azure/cosmos-db/partitioning-overview). Determine the right balance of performance and cost to meet your requirements. 
>
>   Select a throughput allocation schema that suits your workload. Review the benefits of standard and autoscale throughput distributed at the database or container level. Also, consider serverless options when appropriate. [Analyze your workload's traffic patterns](/azure/cosmos-db/how-to-choose-offer#understand-your-traffic-patterns) to select the most suitable throughput allocation scheme.
>
>   Determine a partition key or set of partition keys that have high cardinality and remain unchanged. Use [existing guidance and best practices](/azure/cosmos-db/partitioning-overview#choose-partitionkey) to help select an appropriate partition key. Also, consider your [indexing policy](/azure/cosmos-db/index-policy) when you determine a partition key.
>
> - **Optimize your Azure Cosmos DB data costs.** Take an inventory of data and calculate the expected overall data storage for your workload. The size of both items and indexes influence your data storage cost. Calculate the impact of replication and backup on storage costs.
>
>   Create a strategy to automatically remove older items that are no longer used or necessary. If required, export these items to a lower-cost storage solution before you remove them.
>
> - **Optimize your queries for cost.** Evaluate your most common queries that minimize cross-partition lookups. Use this information to inform the process of selecting a partition key or customizing an indexing policy.
>
>   Use projection to reduce throughput costs of large query result sets. Author queries to project only the minimal number of fields required from a result set. If calculations on fields are necessary, evaluate the throughput cost of performing those calculations server side versus client side.
>
>   Avoid using unbounded cross-partition queries. Evaluate and author queries to ensure that they search within a single logical partition whenever possible. Use query filters to control which logical partitions the query targets. If a query must search across logical partitions, bound the query to only search a subset of logical partitions instead of a full scan.
>
>   Design an indexing policy that considers the common operations and queries in your workload.

### Configuration recommendations

| Recommendation | Benefit |
| --- | --- |
| [Monitor the usage and patterns of RUs per second](/azure/cosmos-db/monitor-normalized-request-units). Use queries and other data research techniques to find antipatterns in your application code. | Using metrics to monitor RU consumption starting early in your Azure Cosmos DB deployment helps you identify inefficient queries and operations that consume excessive RUs. You can optimize queries to lower costs.|
| Customize your [indexing policy](/azure/cosmos-db/nosql/how-to-manage-indexing-policy) to align with your workload. Use an indexing policy based on only the paths that you need to index for your common queries.<br><br>For write-heavy workloads, disable automatic indexing of columns not used in queries.| The default indexing policy indexes all paths in an item and can significantly affect RU consumption and costs. Customizing your indexing policy allows you to tailor the indexing strategy to your specific requirements. This approach helps ensure optimal performance and resource usage.|
| Select [partition keys](/azure/cosmos-db/partitioning-overview) for your workload that evenly distribute throughput consumption and data storage across logical partitions. Avoid hot partitions that receive a disproportionate amount of traffic. Unbalanced partitions can increase throughput costs and transient errors.<br><br>The selection should also minimize the number of unbounded cross-partition queries. Use the most common search queries to determine potential partition keys that likely run only single-partition or bounded cross-partition queries. | Choosing the right partition key evenly distributes data, which reduces the likelihood of hot partitions and optimizes throughput consumption. This approach can significantly improve efficiency and lower costs.|
| Select the right provisioned throughput option for your Azure Cosmos DB instance. Choose between [serverless](/azure/cosmos-db/throughput-serverless) or [provisioned throughput](/azure/cosmos-db/how-to-choose-offer) and manual provisioning or autoscale. Apply these options at the database or container level, depending on the specific needs of your workload.<br><br> Generally, small workloads and dev/test workloads benefit from serverless throughput or manual shared throughput at the database level. Larger, mission-critical workloads might benefit from provisioned throughput at the container level.| Selecting the right provisioned throughput option helps optimize cost by avoiding over-provisioning or under-provisioning. It also reduces operational costs while ensuring that your application can effectively handle varying traffic patterns.|
| Configure the [default consistency level](/azure/cosmos-db/consistency-levels#configure-the-default-consistency-level) for your application. When appropriate, [downgrade the default consistency level](/azure/cosmos-db/nosql/how-to-manage-consistency#override-the-default-consistency-level) in client sessions.<br><br> Consider the higher costs associated with reads at stronger consistency levels. You might not always need to change the standard default consistency level or override it in client sessions.| Choosing the right consistency level for your application ensures that you achieve the desired balance between data consistency, availability, performance, and cost.|
| For dev/test workloads, use the [Azure Cosmos DB emulator](/azure/cosmos-db/local-emulator), which is also available as a [Docker container image](/azure/cosmos-db/docker-emulator-linux).| The emulator reduces costs for dev/test and continuous integration use cases. |
| Use [transactional batch operations](/azure/cosmos-db/transactional-batch). Design partitions to take advantage of transactional batch operations within a logical partition key for inserting data.<br><br>Use batch operations in client-side SDKs to insert, update, or delete multiple documents in a single transaction request.  | Using transactional batch operations in Azure Cosmos DB can help reduce the number of individual requests. This approach helps reduce latency and improve throughput efficiency. |
| Implement [time-to-live (TTL)](/azure/cosmos-db/nosql/how-to-time-to-live) to automatically delete unnecessary data. Export the expired data to a lower-cost storage solution if needed.| Using TTL ensures that your database remains clutter free, which optimizes storage costs. Exporting expired data to a lower-cost storage solution can further reduce expenses while preserving historical data for compliance purposes.|
| Consider an [analytical store](/azure/cosmos-db/analytical-store-introduction) for heavy aggregations. | Azure Cosmos DB analytical store automatically syncs your data to a separate column store to optimize for large aggregations, reporting, and analytical queries. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Cosmos DB for NoSQL.

> [!div class="checklist"]
>
> - **Monitor your Azure Cosmos DB instances.** Include Azure Cosmos DB instances across regions in your workload monitoring solution.
>
>   Create identifiers in the client application to differentiate workloads. Consider flags, such as the user-agent suffix, to identify what workload each log entry or metric should be associated with.
>
>   Draft a log and metrics monitoring strategy to differentiate between different workloads, flag exceptional scenarios, track patterns in exceptions and errors, and track host machine performance.
>
>   Define multiple alerts to monitor throttling, analyze throughput allocation, and track the size of your data.
>
>   Plan expected metric thresholds based on partition and index design. Create a plan to monitor those metrics to determine how close they are to the planned thresholds.
>
> - **Automate deployments through infrastructure as code (IaC).** Incorporate all Azure Cosmos DB changes, including infrastructure deployments and configuration changes, in your code deployment practices.
>
>   Create and enforce best practices to automate the deployment of your Azure Cosmos DB for NoSQL account and resources.
>
>   Ensure that your team uses the same templates to deploy to other nonproduction environments.

### Configuration recommendations

| Recommendation | Benefit |
| --- | --- |
| Ensure that application developers use the latest version of the developer SDK. For more information, see [.NET SDK](/azure/cosmos-db/nosql/sdk-dotnet-v3#recommended-version) and [Java SDK](/azure/cosmos-db/nosql/sdk-java-v4#recommended-version). Each Azure Cosmos DB for NoSQL SDK has a minimum recommended version.| Using the latest versions of the developer SDKs helps maintain the performance, security, and compatibility of your applications.|
| Capture [supplemental diagnostics](/azure/cosmos-db/nosql/troubleshoot-dotnet-sdk) by using the diagnostics injection techniques for each SDK. These techniques add supplemental information about the workload, alongside default metrics and logs. For more information, see [.NET SDK](/azure/cosmos-db/nosql/best-practice-dotnet#capture-diagnostics) and [Java SDK](/azure/cosmos-db/nosql/troubleshoot-java-sdk-v4#enable-client-sice-logging). | By injecting diagnostics, you can capture custom metrics, traces, and logs to improve troubleshooting and optimize your applications. You can monitor specific aspects of your workload that default metrics and logs don't cover.|
| Create [alerts](/azure/cosmos-db/create-alerts) for host machine resources. | Connectivity and availability problems might occur because of client-side host machine problems. Monitor resources such as CPU, memory, and storage on host machines by using client applications that use the Azure Cosmos DB for NoSQL SDKs. |
| Create alerts for throughput throttling by using metrics like the [normalized RU consumption](/azure/cosmos-db/monitor-normalized-request-units). This metric measures the percentage usage of provisioned throughput on a database or container. <br><br> Use alerts to track when this metric exceeds expected thresholds. Over time, review and adjust alerts as you learn more about your workload.| If throughput throttling is consistently at 100%, requests likely return a transient error. By creating alerts based on this metric, you can receive notifications when the usage exceeds your defined thresholds. You can take corrective actions before it affects your application's performance.|
| Use query performance [metrics](/azure/cosmos-db/nosql/query-metrics-performance) to track the performance of your top queries over time. If query performance is poor, troubleshoot performance and apply query best practices.| Performance metrics allow you to better understand how your queries run and where potential bottlenecks exist. This information helps you make informed decisions about indexing policies and query optimizations. |
| Use templates to automatically deploy account resources. Consider [Azure Resource Manager](/azure/cosmos-db/nosql/quickstart-template-json), [Bicep](/azure/cosmos-db/nosql/quickstart-template-bicep), or [Terraform](/azure/cosmos-db/nosql/quickstart-terraform) templates to automate the deployment of your account and subsequent resources.| IaC templates help define your infrastructure in a descriptive model. They facilitate repeatability and consistency and help maintain uniform and predictable workload environments. |
| Track [key metrics](/azure/cosmos-db/monitor-reference) to identify common problems in your workload like RU usage by partition, throttling, and request volumes by type.| Monitoring these key metrics helps you understand how the database performs under various conditions. Monitoring also helps identify common problems so that you can take corrective action.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Azure Cosmos DB for NoSQL.

> [!div class="checklist"]
>
> - **Plan and proactively manage your Azure Cosmos DB capacity.** Define a performance baseline for your application. Measure how many concurrent users and transactions you might need to handle. Consider workload characteristics such as your average user flow, common operations, and spikes in usage.
>
>   Research your target users. Determine which Azure regions are closest to them.
>
> - **Optimize your data design.** Design items so their corresponding JSON documents are as small as possible. Consider splitting data across multiple items if necessary.
>
>   Keep items less than **100 KB** in size. Larger items consume more throughput for common read and write operations. Queries on larger items that project all fields can also add significant throughput cost.
>
> - **Select the right tier, features, and billing model.** Determine if your workload requires an analytical store. Consider analytical stores and services like [Azure Synapse Link](/azure/cosmos-db/synapse-link) for complex queries.
>
> - **Optimize the deployment model.** Deploy Azure Cosmos DB for NoSQL to regions closest to your end users. | Reduce latency by deploying Azure Cosmos DB for NoSQL to the regions closest to your end users as much as possible.
>
> - **Optimize queries for performance.** Review the [query performance tips](/azure/cosmos-db/nosql/performance-tips-query-sdk) and apply the strategies that apply to your use cases.
>
>   Identify queries that use one or more ordering fields. Also, identify operations that affect multiple fields. Include these fields explicitly in the indexing policy design.
>
>   Design large workloads to use bulk operations whenever possible.
>
>   Identify queries on child arrays and determine if they're candidates for [more efficient subqueries](/azure/cosmos-db/nosql/query/subquery).
>
>   Research your most common and complex queries. Identify queries that use multiple lookups, joins, or aggregates. Consider these queries in any design considerations for the partition key or indexing policy.
>
>   For the most common queries, determine the number of results that you expect for each page. This number helps you formalize a buffered item count for prefetched results.
>
> - **Optimize code logic.** Use the singleton pattern for the `CosmosClient` class in most SDKs. Use the client class in most SDKs as a singleton. The client class manages its own lifecycle and is designed to not be disposed. Constantly creating and disposing instances can result in reduced performance.

### Configuration recommendations

| Recommendation | Benefit |
| --- | --- |
| Use tools like the [capacity calculator](https://cosmos.azure.com/capacitycalculator/) to determine the amount of throughput required for your performance baseline. Use features like [autoscale](/azure/cosmos-db/nosql/how-to-provision-autoscale-throughput) to scale your actual throughput to more closely match your actual workload. Monitor your actual throughput consumption afterwards and make adjustments. | Using the capacity calculator helps you make informed decisions about provisioning resources initially. You can ensure that your database can handle the expected load without unnecessary costs. Autoscale throughput automatically adjusts the provisioned throughput to match the actual workload needs.  |
| Use optimization techniques on the client and server sides when appropriate. Take advantage of the built-in [integrated cache](/azure/cosmos-db/integrated-cache). Configure the SDK to manage how many items are prefetched, or *buffered*, and returned for each page. | Code optimization techniques can improve the user experience and make your client and server side application more efficient by helping reduce the RU charges for repeated reads and queries. These benefits can reduce costs.|
| Use [Cosmos DB Query Advisor](/cosmos-db/query/advisor) for recommendations for optimizing query performance, indexing strategies, and resource utilization based on actual usage patterns and workload characteristics. | You can programmatically identify slow queries, optimize indexing strategies, and potentially reduce RU consumption.|
| Use [global distribution](/azure/cosmos-db/distribute-data-globally) and deploy Azure Cosmos DB instances in all regions closest to your end users. | Deploying your database to the regions closest to your end users helps reduce *read* latency which can improve user experience. Read replication enables performant read operations regardless of how the write operations are configured. |
| Use [multi-region writes](/azure/cosmos-db/nosql/how-to-multi-master) if your clients are geographically distributed. | Reduces *write* latency by allowing the client to write to a region that is closest to it. Your workload must support conflict resolution logic and your application needs to expect eventual consistency behavior. |
| Configure the SDK for [Direct mode](/azure/cosmos-db/nosql/sdk-connection-modes) for the best performance. This mode allows your client to open Transmission Control Protocol (TCP) connections directly to partitions in the service and send requests directly with no intermediary gateway.| Direct mode provides better performance because there are fewer network hops. |
| Use the [bulk feature](/azure/cosmos-db/nosql/tutorial-dotnet-bulk-import) of client SDKs in scenarios that require high throughput. The bulk feature automatically manages and batches operations to maximize throughput.| Using the bulk feature reduces the number of back-end requests and efficiently distributes tasks across partitions. This approach improves performance and resource usage.|
| Disable indexing for bulk operations. If there are several insert, replace, or upsert operations, disable indexing to improve the speed of the operation while using the [bulk support](/azure/cosmos-db/nosql/tutorial-dotnet-bulk-import) of the corresponding SDK. You can immediately reenable indexing later. | Disabling indexing during bulk operations reduces the overhead associated with maintaining the index. You can better utilize resources for bulk operations, which accelerates data insertion and updates. |
| Create [composite indexes](/azure/cosmos-db/index-overview#composite-indexes) for fields in complex operations. Consider using composite indexes for `ORDER BY` statements that have multiple fields.| Composite indexes can increase the efficiency of operations on multiple fields, which makes data retrieval fast and efficient. |
| Optimize host client machines for the SDKs. For most common cases, use at least four cores and eight GB of memory on 64-bit host machines that use the SDKs for [.NET](/azure/cosmos-db/nosql/best-practice-dotnet#checklist) or [Java](/azure/cosmos-db/nosql/best-practice-java).<br><br>Enable [accelerated networking](/azure/virtual-network/create-vm-accelerated-networking-powershell) on host machines.| Using machines that have higher specifications ensures that the SDKs can handle more operations simultaneously. Accelerated networking can help reduce latency, which increases response time. |
| Use subqueries strategically to optimize queries that join large data sets. [Optimize self-join expressions](/azure/cosmos-db/nosql/query/subquery#optimize-self-join-expressions) by using subqueries to filter the arrays before [joining arrays within the item](/azure/cosmos-db/nosql/query/join).<br><br>For cross-partition queries, optimize your query to include a filter on the partition key to optimize the routing of your query to the least amount of partitions possible. | Queries that join child arrays can increase in complexity if multiple arrays are involved and not filtered. By employing subqueries, arrays can be filtered before joining them, which increases the efficiency of self-join expressions.|
| Use [analytical workloads](/azure/cosmos-db/analytical-store-introduction) for the most complex queries.<br><br> If you run frequent aggregations or join queries over large containers, consider enabling the analytical store and doing queries in Azure Synapse Analytics. | Analytical workloads are isolated from transactional workloads and optimized for handling complex queries, which prevents performance degradation and ensures faster results. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Cosmos DB for NoSQL and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- The Azure Cosmos DB account is deployed to at least two regions.

- Azure Cosmos DB database accounts are appropriately configured for zone redundancy.
- Azure Cosmos DB database accounts should have local authentication methods disabled so that Microsoft Entra identities are required for authentication.
- Public network access is disabled so that your Azure Cosmos DB account isn't exposed on the public internet.
- The maximum throughput should be restricted when you create Azure Cosmos DB databases and containers through the resource provider.

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Cosmos DB for NoSQL](/azure/cosmos-db/security-controls-policy) and other policies that might affect the security of the data storage.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Related resources

- [Multitenancy and Azure Cosmos DB](/azure/architecture/guide/multitenant/service/cosmos-db)
- [Common Azure Cosmos DB use cases](/azure/cosmos-db/use-cases)
- [Serverless database computing by using Azure Cosmos DB](/azure/architecture/solution-ideas/articles/serverless-apps-using-cosmos-db)

<!-- Updated: August 17, 2025 for Azure Update 470102 -->
<!-- Updated: November 21, 2025 for Azure Update 520696 -->

