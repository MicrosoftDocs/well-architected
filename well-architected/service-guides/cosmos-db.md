---
title: Architecture Best Practices for Azure Cosmos DB for NoSQL
description: Design considerations and recommendations for each pillar as related to solutions using Azure Cosmos DB for NoSQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: conceptual
ms.date: 02/20/2025
products:
  - azure-cosmos-db
---

# Architecture best practices for Azure Cosmos DB for NoSQL

Azure Cosmos DB is a fully managed database solution that can host multiple database types like NoSQL, relational, vector, and table, accommodating most use cases for your workload. The recommendations offered in this guide focus on the [Cosmos DB for NoSQL](/azure/cosmos-db/nosql/) variant, though there are some foundational recommendations that can be applied to other variants.

This article assumes that as an architect, you've reviewed the [Azure data options](/azure/architecture/guide/technology-choices/data-options) and chosen Azure Cosmos DB for NoSQL as the data store for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Service Fabric and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>

**TODO:** removed the reference architectures because the ones linked from here have been removed from AAC. AAC doesn't seem to have any reference architecture that showcases Cosmos DB and is representative of best practices recommended here.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](../reliability/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

> [!div class="checklist"]
>
> - **Strive for a simple, easy to manage design:** Avoid adopting functionality that unnecessarily adds complexity to your workload, like multi-master writes and custom indexing. While Cosmos DB supports multi-master writes and custom indexing, they can add significant complexity and operational burden, so carefully evaluate whether those and other advanced features are necessary for your workload's requirements.
> - Evaluate flows as they relate to Cosmos DB and consider whether there are critical flows that need specific configurations.
>
>   **TODO:moved from recommendations** Identify critical flows and any specific configurations needed for them. Capture processing requirements for your flows to help identify which may need special treatment. Examples include flows that are more sensitive to latency issues or flows that require higher consistency levels than others.
>
> - Consider how your selected [consistency level](/azure/cosmos-db/consistency-levels) and replication mode [impacts the Recovery point objective (RPO)](/azure/cosmos-db/consistency-levels#rto) in a region-wide outage.
> - Design your database account deployment so it spans at least two regions in Azure. Additionally, distribute your account across multiple availability zones when offered within your Azure region.
> - Evaluate the multi-region and single-region write strategies for your workload. For single-region write, design your workload to have at least a second read region for failover. Enable auto-failover for single-region write and multi-region read scenarios. For multi-region write, compare the tradeoffs in complexity and consistency against the advantages of writing to multiple regions. Review [expectations during a regional outage for single-region and multi-region write accounts](/azure/cosmos-db/high-availability#what-to-expect-during-a-region-outage).
> - Enable [service-managed failover](/azure/cosmos-db/high-availability#availability) for your account.
> - Design an end-to-end test of high availability for your application.
> - Walk through [common backup processes](/azure/cosmos-db/continuous-backup-restore-introduction) including, but not limited to; point-in-time restore, recovering from accidental destructive operations, restoring deleted resources, and restoring to another region at a point-in-time. Configure account with [continuous backup](/azure/cosmos-db/online-backup-and-restore), choosing the appropriate retention period based on your business requirements.
> - Explore the [designing resilient applications guide](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications), review the [default retry policy](/azure/architecture/best-practices/retry-service-specific#azure-cosmos-db) for the SDKs, and plan for [custom handling for specific transient errors](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications#should-my-application-retry-on-errors). These guides will give best practices to make application code resilient to transient errors.
>

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| **TODO:link-added** Distribute your Azure Cosmos DB account across [availability zones](/azure/reliability/reliability-cosmos-db-nosql?context=%2Fazure%2Fcosmos-db%2Fnosql%2Fcontext%2Fcontext) (when available). | **TODO:benefit-reviewed** Availability zones provide distinct power, networking, and cooling isolating hardware failures to a subset of your replicas. Azure Cosmos DB has multiple replicas that span across a single random availability zone when the availability zones feature isn't used. If the availability zone feature is used, replicas span across multiple availability zones. |
| **TODO:link-added** Configure your Azure Cosmos DB account to span at least [two regions](/azure/cosmos-db/nosql/how-to-multi-master). | **TODO:benefit-reviewed** Spanning multiple regions prevents your account from being entirely unavailable if there's an isolated region outage. |
| **TODO:link-added, merged two entries** Enable [service-managed failover](/azure/cosmos-db/how-to-manage-database-account#enable-service-managed-failover-for-your-azure-cosmos-db-account) for your account. Understand the tradeoffs with service-managed failover and plan for forced failover if necessary.</br></br>Temporarily disable service-managed failover to validate the end-to-end high availability of your application with a manual failover started using a script or the Azure portal.| **TODO:benefit-edited** Service-managed failover allows Azure Cosmos DB to change the write region of a multiple-region account to preserve availability. This change occurs without user interaction.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of your workload.

### Design checklist

> [!div class="checklist"]
> - **TODO:moved from recommendations** see [security checklist for Azure databases](/azure/security/fundamentals/database-security-checklist)
> - **TODO:moved from recommendations** Implement, at a minimum, the data protection and identity management security baselines. Go through the [security baseline](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline) including [identity management](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#identity-management) and [data protection](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#data-protection). Implement the recommendations to secure your Azure Cosmos DB account.
> - Reduce surface attack area by designing to use private endpoints in accordance with the [security baseline](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#network-security) for Azure Cosmos DB.
> - Create roles, groups, and assignments for control-plane and data-plane access to your account per the principle of [least-privilege access](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#privileged-access). Consider [disabling key-based authentication](/azure/cosmos-db/how-to-setup-rbac#disable-local-auth).
> - Assess service-level [compliance](/azure/cosmos-db/compliance) and [certifications](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) in the context of current global personal data requirements.
> - [Encrypt data](/azure/cosmos-db/database-encryption-at-rest) at-rest or in-motion using service-managed keys or customer-managed keys (CMKs).
> - Audit user access, security breaches, and resource operations with [control plane logs.](/azure/cosmos-db/audit-control-plane-logs).
> - Monitor data egress, data changes, usage, and latency with [data plane metrics](/azure/cosmos-db/use-metrics).
> - **TODO:moved from recommendations** Follow best software development practices for secure access to data. Follow secure coding practices and perform secure code reviews when developing applications that interact with Azure Cosmos DB.

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| **TODO:link-added** Disable public endpoints and use [private endpoints](/azure/cosmos-db/how-to-configure-private-endpoints?tabs=arm-bicep) whenever possible. | **TODO:benefit-edited** Avoid leaving unnecessary or unused public endpoints to reduce the surface area susceptible to attacks on your account. |
| **TODO:link-added** Use [role-based access control](/azure/cosmos-db/nosql/security/how-to-grant-data-plane-role-based-access) to limit control-plane access to specific identities and groups and within the scope of well-defined assignments. |  **TODO:benefit-reviewed** Use role-based access control to prevent unintended access to your account. Assign appropriate roles and permissions to users or applications accessing Azure Cosmos DB. |
| **TODO:link-added** Create [virtual network](/azure/cosmos-db/how-to-configure-vnet-service-endpoint) endpoints and rules to limit access to the account. Use network security groups (NSGs) to control inbound and outbound traffic to and from the Azure Cosmos DB resources.| **TODO:benefit-reviewed** Virtual network service endpoints and firewall rules help restrict access to your Azure Cosmos DB account and  helps protect your data from unauthorized access. |
| **TODO:portion moved to echcklist** Protect against common security vulnerabilities like injection attacks, cross-site scripting (XSS), or insecure direct object references (IDOR). Implement input validation, [parameterized queries](/azure/cosmos-db/nosql/query/parameterized-queries), and appropriate error handling for common [HTTP status codes](/en-us/rest/api/cosmos-db/http-status-codes-for-cosmosdb) to prevent security risks. | **TODO:benefit-added** Input validation helps prevent malicious data from being processed by your application, blocking potentially harmful data that could lead to security breaches or data corruption.</br></br>Proper error handling enables your application to respond to errors in a controlled manner, preventing the exposure of sensitive information.|
| **TODO:link-added** [Monitor control-plane logs](/azure/cosmos-db/audit-control-plane-logs) to detect unauthorized modifications to your Cosmos DB account. For more information.| **TODO:benefit-edited** Monitoring helps you track access patterns and audit logs, ensuring that your database remains secure and compliant with relevant data protection regulations. Monitoring data-plane metrics can also help identify unfamiliar patterns that might reveal a security breach. |
| **TODO:link-added** Enable [Microsoft Defender for Azure Cosmos DB](/azure/defender-for-cloud/defender-for-databases-enable-cosmos-protections) so that you can trigger security alerts when anomalies in activity occur. | **TODO:benefit-edited** Microsoft Defender detects attempts to exploit databases in your Azure Cosmos DB for NoSQL account. Defender detects potential SQL injections, suspicious access patterns, and other potential exploitation. |

## Cost optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to your workload.

> [!div class="checklist"]
>
> - Design an indexing policy that's considers the operations and queries you commonly make in your workload.
> - Determine a partition key or set of partition keys which have a value  that has high cardinality and does not change. Use the [existing guidance and best practices](/azure/cosmos-db/partitioning-overview#choose-partitionkey) to help select an appropriate partition key. Also, consider your [indexing policy](/azure/cosmos-db/index-policy) when determining a partition key.
> - Select a throughput allocation schema that's appropriate for your workload. Review the benefits of standard and autoscale throughput distributed at the database or container level. Also, consider serverless when appropriate. [Review your workload's traffic patterns](/azure/cosmos-db/how-to-choose-offer#understand-your-traffic-patterns) in the context of selecting a throughput allocation scheme.
> - Consider consistency levels as they relate to your workload. Also, consider if client sessions should alter the default consistency level.
> - Calculate the expected overall data storage for your workload. The size of items and indexes all influence your data storage cost. Calculate the impact of replication and backup on storage costs.
> - Create a strategy to automatically remove older items that are no longer used or necessary. If required, export these items to a lower-cost storage solution before they are removed.
> - **TODO:in-progress** **Optimize queries:** Evaluate your most common queries that minimize cross-partition lookups. Use this information to inform the process of selecting a partition key or customizing an indexing policy.
>
>   Use projection to reduce throughput costs of large query result sets. Author queries to only project the minimal number of fields required from a result set. If calculations on fields are necessary, evaluate the throughput cost of performing those calculations server-side versus client-side.
>
>   Avoid using unbounded cross-partition queries. Evaluate and author queries to ensure they search within a single logical partition whenever possible. Use query filters to control which logical partitions the query targets. If a query must search across logical partitions, bound the query to only search a subset of logical partitions instead of a full scan.

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| **TODO:link-added** [Monitor RU/s utilization and patterns](/azure/cosmos-db/monitor-normalized-request-units), use queries and other data research techniques to find antipatterns in your application code. | **TODO:benefit-edited** Use metrics to monitor RU consumption from the very beginning of your solution to help in identify inefficient queries and operations that consume excessive RU, allowing you to optimize them to lower cost.|
| **TODO:link-added** Customize your [indexing policy](/azure/cosmos-db/nosql/how-to-manage-indexing-policy) to map to your workload. Use an indexing policy designed based on only the paths that you need to index for your common queries.</br></br>For write-heavy workloads, disable automatic indexing of columns not used in queries.| **TODO:benefit-edited** The default indexing policy indexes all paths in an item and can have significant impacts to RU consumption and costs. Customizing your indexing policy allows you to tailor the indexing strategy to your specific  requirements, ensuring optimal performance and resource utilization.|
| **TODO:link-added** Select [partition key\[s\]](/azure/cosmos-db/partitioning-overview) for your workload that distribute throughput consumption and data storage evenly across logical partitions. Avoid hot partitions that receive a disproportionate amount of traffic, as unbalance partitions can increase throughput costs and transient errors.</br></br>The selection should also minimize the number of unbounded cross-partition queries. Use the most common search queries to determine potential partition key\[s\] that likely executes only single-partition or bounded cross-partition queries. | **TODO:benefit-added** Choosing the right partition key ensures that data is evenly distributed, reducing the likelihood of hot partitions and optimizing throughput consumption. This can significantly improve performance and leads to lower costs.|
| **TODO:link-added** Select the right provisioned throughput option for your Azure Cosmos DB workload. Use [serverless](/azure/cosmos-db/throughput-serverless) or [provisioned throughput](/azure/cosmos-db/how-to-choose-offer), manual provisioning or autoscale, at the database or container level aligned with the specific needs of your workload.</br></br> Generally, smaller and dev/test workloads might benefit from serverless throughput or manual shared throughput at the database level. Larger, mission-critical workloads might benefit from provisioned throughput assigned at the container level.| **TODO:benefit-edited** Selecting the right provisioned throughput option for your Azure Cosmos DB workload helps optimize cost by avoiding over-provisioning or under-provisioning, and reducing operational costs while still ensuring your application can handle varying traffic patterns effectively.|
| Configure the [default consistency level](/azure/cosmos-db/consistency-levels#configure-the-default-consistency-level) for your application. When appropriate, [downgrade the default consistency level](/azure/cosmos-db/nosql/how-to-manage-consistency#override-the-default-consistency-level) in client sessions.<br><br> Consider the higher costs associated with reads at stronger consistency levels. You might not always need to change the standard default consistency level or override it in client sessions.| **TODO:benefit-edited** Choosing the right consistency level for your application ensures that you achieve the desired balance between data consistency, availability, and performance, optimizing cost.|
| For dev/test workloads, use the [Azure Cosmos DB emulator](/azure/cosmos-db/local-emulator). Also available as a [Docker container image](/azure/cosmos-db/docker-emulator-linux).| **TODO:benefit-reviewed** The emulator is an option for dev/test and continuous integration that can save on the costs of these common workloads for your development team.|
| **TODO:link-added** Use [transactional batch operations](/azure/cosmos-db/nosql/transactional-batch). Design partitions to take advantage of transactional batch operations within a logical partition key for inserting.</br></br>Use batch operations in client-side SDKS for inserting, updating, or deleting multiple documents in a single transaction request.  | **TODO:benefit-edited** Using transactional batch operations in Azure Cosmos DB can help reduce the number of individual requests, reducing latency and leading to better throughput efficiency. |
| **TODO:link-added** Implement [time-to-live (TTL)](/azure/cosmos-db/nosql/how-to-time-to-live) to automatically delete data that's no longer needed. If necessary, export the expired data to a lower-cost storage solution.| **TODO:benefit-edited** Using TTL ensures that your database remains clutter-free, optimizing storage cost. Exporting expired data to a lower-cost storage solution can further reduce expenses while preserving historical data for compliance purposes.|
| **TODO:link-added** Consider an [analytical store](/en-us/azure/cosmos-db/analytical-store-introduction) for heavy aggregations. | **TODO:benefit-reviewed** Azure Cosmos DB analytical store automatically syncs your data to a separate column store to optimize for large aggregations, reporting, and analytical queries. |

## Operational excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

> [!div class="checklist"]
>
> - Draft a log and metrics monitoring strategy to differentiate between different workloads, flag exceptional scenarios, track patterns in exceptions/errors, and track host machine performance.
> - Design large workloads to use bulk operations whenever possible.
> - Define multiple alerts to monitor throttling, analyze throughput allocation, and track the size of your data.
> - **TODO:in-progress** Design a monitoring strategy for availability of your solution across regions.
>
>   Create identifiers in the client application to differentiate workloads. Consider flags, such as the user-agent suffix, to identify what workload each log entry or metric should be associated with.
> - Create and enforce best practices for automating the deployment of your Azure Cosmos DB for NoSQL account and resources.
> - Plan expected metric thresholds based on partition and index design. Ensure that there's a plan to monitor those metrics to determine how close they are to the planned thresholds.
> - **TODO: add ioc** 
>   Ensure that your team is using the same templates to deploy to other nonproduction environments.

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| **TODO:link-added** Ensure application developers are using the latest version of the developer SDK. For more information, see [.NET SDK](/azure/cosmos-db/nosql/sdk-dotnet-v3#recommended-version) and [Java SDK](/azure/cosmos-db/nosql/sdk-java-v4#recommended-version).Each Azure Cosmos DB for NoSQL SDK has a minimum recommended version.|**TODO:benefit-added** Using the latest versions of the developer SDKs will help maintain the performance, security, and compatibility of your applications.|
| **TODO:link-added** Capture [supplemental diagnostics](/azure/cosmos-db/nosql/troubleshoot-dotnet-sdk) using the diagnostics injection techniques for each SDK to add supplemental information about the workload, alongside default metrics and logs. For more information, see [.NET SDK](/azure/cosmos-db/nosql/best-practice-dotnet#capture-diagnostics) and [Java SDK](/azure/cosmos-db/nosql/troubleshoot-java-sdk-v4#enable-client-sice-logging). | **TODO:benefit-added** By injecting diagnostics, you can capture custom metrics, traces, and logs, to improve your ability to troubleshoot and optimize your applications. You can monitor specific aspects of your workload that are not covered by default metrics and logs.|
| **TODO:link- ///invalid link///** Create [alerts](/azure/cosmos-db/create-alerts) associated with host machine resources. | Connectivity and availability issues might occur due to client-side host machine issues. Monitor resources such as CPU, memory, and storage on host machines with client applications using the Azure Cosmos DB for NoSQL SDKs. |
| **TODO:link-moved** Use the [bulk features](/azure/cosmos-db/nosql/tutorial-dotnet-bulk-import) of client SDKs in scenarios that require high throughput. The bulk feature automatically manages and batches operations to maximize throughput.| **TODO:benefit-added** Using the bulk features reduces the number of backend requests and efficiently distributing tasks across partitions, leading to improved performance and better resource utilization.|
| **TODO:link-moved** Create alerts for throughput throttling using metrics such as the [Normalized RU Consumption](/azure/cosmos-db/monitor-normalized-request-units), which measures the percentage utilization of provisioned throughput on a database or container. </br></br> Use alerts to track this metric passing beyond expected thresholds. Over time, review and adjust alerts as you learn more about your workload.| **TODO:benefit-edited** If throughput throttling is consistently at 100%, requests likely return a transient error. By creating alerts based on this metric, you can receive notifications when the utilization exceeds your defined thresholds, allowing you to take corrective actions before it impacts your application's performance.|
| **TODO:link-added** Use query performance [metrics](/azure/cosmos-db/nosql/query-metrics-performance) to track the performance of your top queries over time. If query performance is poor, troubleshoot performance and apply query best practices.| **TODO:benefit-edited** Performance metrics allow you to better understand how your queries are executed and where potential bottlenecks may exist. This information helps you make informed decisions about indexing policies and query optimizations. |
| Use templates to automatically deploy account resources. Consider [Azure Resource Manager](/azure/cosmos-db/nosql/quickstart-template-json), [Bicep](/azure/cosmos-db/nosql/quickstart-template-bicep), or [Terraform](/azure/cosmos-db/nosql/quickstart-terraform) templates to automate the deployment of your account and subsequent resources.| **TODO:benefit-added** IaC templates enable you to define your infrastructure in a descriptive model, facilitating repeatability and consistency, helping maintain a uniform and predictable workload environments. |
| **TODO:link-moved** Track [key metrics](/azure/cosmos-db/monitor-reference) to identify common problems in your workload, including, but not limited to; RU utilization, RU utilization by partition, throttling, and request volumes by type.| **TODO:benefit-added** Monitoring these key metrics help you to understand how the database is performing under various conditions,  identify common problems, and take corrective action.|

## Performance efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

> [!div class="checklist"]
>
> - Define a performance baseline for your application. Measure how many concurrent users and transactions you might need to handle. Consider workload characteristics such as your average user flow, common operations, and spikes in usage.
> - Research your most common and most complex queries. Identify queries that use multiple lookups, joins, or aggregates. Consider these queries in any design considerations for the partition key or indexing policy.
> - For the most common queries, determine the number of results you expect per page. This number will help formalize a buffered item count for prefetched results.
> - Research your target users. Determine which Azure regions are closest to them.
> - Identify queries that use one or more ordering fields. Also, identify operations that impact multiple fields. Include these fields explicitly in the indexing policy design.
> - **TODO:moved from perf recommendations (query perf optimization item)** Use the singleton pattern for the `CosmosClient` class in most SDKs. Use the client class in most SDKs as a singleton. The client class manages its own lifecycle and is designed to not be disposed. Constantly creating and disposing of instances can result in reduced performance.
> - Design items so their corresponding JSON documents are as small as possible. Considering splitting data cross multiple items if necessary.
> - **TODO:moved from perf recommendations (data design item)** Keep item sizes less than **100 KB** in size. Larger items consume more throughput for common read and write operations. Queries on larger items that project all fields can also have a significant throughput cost.
> - Identify queries on child arrays and determine if they are candidates for [more efficient subqueries](/azure/cosmos-db/nosql/query/subquery).
> - Determine if your workload requires an analytical store. Consider analytical stores and services like [Azure Synapse Link](/azure/cosmos-db/synapse-link) for extremely complex queries.
> - **TODO:moved from opex recommendations** For more information, see [query performance tips](/azure/cosmos-db/nosql/performance-tips-query-sdk)
> - **TODO:moved from perf recommendations** Deploy Azure Cosmos DB for NoSQL to regions closest to your end users. | Reduce latency by deploying Azure Cosmos DB for NoSQL to the regions closest to your end users as much as possible.

| Recommendation | Benefit |
| --- | --- |
|  **TODO:link-moved** Use tools like the [capacity calculator](https://cosmos.azure.com/capacitycalculator/) to determine the amount of throughput required for your performance baseline. Use features like [autoscale](/azure/cosmos-db/nosql/how-to-provision-autoscale-throughput) to scale your actual throughput to more closely match your actual workload. Monitor your actual throughput consumption afterwards and make adjustments. | **TODO:benefit-added** Using the capacity calculator helps you make informed decisions about provisioning resources initially, ensuring that your database can handle the expected load without unnecessary costs. Autoscale throughput automatically adjusts the provisioned throughput to match the actual workload needs.  |
|  **TODO:link-moved** Use optimization techniques on the client and server sides when appropriate. Take advantage of the built-in [integrated cache](/azure/cosmos-db/integrated-cache). Configure the SDK to manage how many items are prefetched (buffered) and returned for each page. | **TODO:benefit-added** Code optimization techniques can improve the user experience and make your client and server side application more efficient by helping reduce the RU charges for repeated reads and queries, potentially resulting insignificant cost savings.|
| **TODO:link-moved** Deploy Azure Cosmos DB for NoSQL to the regions closest to your end users. Configure the ([.NET](/azure/cosmos-db/nosql/best-practice-dotnet)/[Java](/azure/cosmos-db/nosql/best-practice-java)) SDK to prefer regions closer to your end user.</br></br>Take advantage of read replication to provide performant read performance regardless of how you configure write (single or multiple regions). | **TODO:benefit-added**  Deploying your database to the regions closest to your end users helps reduce latency, improving user experience. Read replication enables performant read operations regardless of how the write operations are configured.|
| Configure the SDK for [Direct mode](/azure/cosmos-db/nosql/sdk-connection-modes) for the best performance. This mode allows your client to open TCP connections directly to partitions in the service and send requests directly with no intermediary gateway.|**TODO:benefit-edited** Direct mode offers better performance because there are fewer network hops. |
| Disable indexing for bulk operations. If there are many insert/replace/upsert operations, disable indexing to improve the speed of the operation while using the [bulk support](/azure/cosmos-db/nosql/tutorial-dotnet-bulk-import) of the corresponding SDK. Indexing can be immediately reenabled later. | **TODO:benefit-added** Disabling indexing during bulk operations reduces the overhead associated with maintaining the index. Resources are better utilized for the bulk operations, resulting in faster data insertion and updates. |
| **TODO:link-moved** Create [composite indexes](/azure/cosmos-db/index-overview#composite-indexes) for fields that are used in complex operations. Consider using composite indexes for `ORDER BY` statements with multiple fields.| **TODO:benefit-edited** Composite indexes can increase the efficiency of operations on multiple fields by orders of magnitude, resulting in faster and more efficient data retrieval. |
| Optimize host client machines for the SDKs. For most common cases, use at least 4-cores and 8-GB memory on 64-bite host machines using the SDKs ([.NET](/azure/cosmos-db/nosql/best-practice-dotnet#checklist)/[Java](/azure/cosmos-db/nosql/best-practice-java)).</br></br>Enable [accelerated networking](/azure/virtual-network/create-vm-accelerated-networking-powershell) on host machines.| **TODO:benefit-added** Using machines with higher specifications ensures that the SDKs can handle more operations simultaneously. Accelerated networking can help reduce latency, resulting in quicker responses. |
| **TODO:link-moved** Use subqueries strategically to optimize queries that join large data sets. [Optimize self-join expressions](/azure/cosmos-db/nosql/query/subquery#optimize-self-join-expressions) by using subqueries to filter the arrays before [joining arrays within the item](/azure/cosmos-db/nosql/query/join).</br></br>For cross-partition queries, optimize your query to include a filter on the partition key to optimize the routing of your query to the least amount of partitions possible. | **TODO:benefit-edited**  Queries that join child arrays can increase in complexity if multiple arrays are involved and not filtered. By employing subqueries, arrays can be filtered before joining them, increasing the efficiency of self-join expressions.|
| Use [analytical workloads](/azure/cosmos-db/analytical-store-introduction) for the most complex queries.</br></br> If you run frequent aggregations or join queries over large containers, consider enabling the analytical store and doing queries in Azure Synapse Analytics. | **TODO:benefit-edited** Analytical workloads are isolated from transactional workloads and optimized for handling complex queries, preventing performance degradation and ensuring faster results. |

### Azure Policy definitions

- [Policy: Require at least two regions](https://github.com/Azure/Community-Policy/blob/main/policyDefinitions/Cosmos%20DB/audit-geo-replication-for-azure-cosmos-db/azurepolicy.json)
- [Policy: Enable service-managed failover](https://github.com/Azure/Community-Policy/blob/main/policyDefinitions/Cosmos%20DB/audit-automatic-failover-for-azure-cosmos-db/azurepolicy.json)
- [Policy: Require specific deployment regions](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json)

### Azure Policy definitions

- [Policy: Enable Microsoft Defender](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/MDC_Microsoft_Defender_Azure_Cosmos_DB_Audit.json)
- [Policy: Require a virtual network service endpoint](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json)
- [Policy: Disable local authentication](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableLocalAuth_AuditDeny.json)
- [Policy: Require firewall rules](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json)

### Azure Policy definitions

- [Policy: Restrict the maximum allowed throughput](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_MaxThroughput_Deny.json)

### Azure Policy definitions

- [Policy: Email notification for high severity alerts](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json)

### Azure Policy definitions

- [Policy: Enable auditing of Azure Synapse Analytics](/azure/azure-sql/database/auditing-overview)

## Related content

### Azure Architecture Center guidance

- [Multitenancy and Azure Cosmos DB](/azure/architecture/guide/multitenant/service/cosmos-db)
- [Visual search in retail with Azure Cosmos DB](/azure/architecture/industries/retail/visual-search-use-case-overview)
- [Gaming using Azure Cosmos DB](/azure/architecture/solution-ideas/articles/gaming-using-cosmos-db)
- [Serverless apps using Azure Cosmos DB](/azure/architecture/solution-ideas/articles/serverless-apps-using-cosmos-db)
- [Personalization using Azure Cosmos DB](/azure/architecture/solution-ideas/articles/personalization-using-cosmos-db)

### Cloud Adoption Framework guidance

- [Batch Data application with Azure Cosmos DB](/azure/cloud-adoption-framework/scenarios/cloud-scale-analytics/architectures/data-landing-zone-data-products#batch-data-application)

## Next steps

> [!div class="nextstepaction"]
> [Deploy an Azure Cosmos DB for NoSQL account using the a Bicep template](/azure/cosmos-db/nosql/quickstart-template-bicep)
