---
title: Data platform considerations for mission-critical workloads on Azure
description: This design area provides key considerations and recommendations to inform the selection of an optimal data platform
author: calcof
ms.author: calcof
ms.date: 02/02/2022
ms.topic: concept-article
---


# Data platform considerations for mission-critical workloads on Azure

The selection of an effective application data platform is a further crucial decision area, which has far-reaching implications across other design areas. Azure ultimately offers a multitude of relational, non-relational, and analytical data platforms, which differ greatly in capability. It's therefore essential that key non-functional requirements be fully considered alongside other decision factors such as consistency, operability, cost, and complexity. For example, the ability to operate in a multi-region write configuration will have a critical bearing on suitability for a globally available platform.

This design area expands on [application design](mission-critical-application-design.md), providing key considerations and recommendations to inform the selection of an optimal data platform.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)
>

## Data platform selection criteria

Evaluate data requirements across capacity, throughput, data model, consistency, and governance before selecting data technologies.

### Design Considerations

**Capacity and growth**

- Existing (if any) and expected future data volumes based on forecasted data growth rates aligned with business objectives and plans.
  - Data volume should encompass the data itself and indexes, logs, telemetry, and other applicable datasets.
  - Large business-critical and mission-critical applications typically generate and store high volumes (GB and TB) on a daily basis.
  - There can be significant cost implications associated with data expansion.

- Data volume may fluctuate due to changing business circumstances or housekeeping procedures.

- Data volume can have a profound impact on data platform query performance.

- There can be a profound impact associated with reaching data platform volume limits.
  - *Will it result in downtime? and if so, for how long?*
  - *What are the mitigation procedures? and will mitigation require application changes?*
  - *Will there be a risk of data loss?*

- Features such as Time to Live (TTL) can be used to manage data growth by automatically deleting records after an elapsed time, using either record creation or modification.
  - For example, Azure Cosmos DB provides a built-in [TTL](/azure/cosmos-db/time-to-live) capability.

**Throughput and latency**

- The speed with which data is emitted from various application components, and the throughput requirements for how fast data needs to be committed and retrieved are critical to determining an optimal data technology for key workload scenarios.
  - The nature of throughput requirements will differ by workload scenario, such as those that are read-heavy or write-heavy.
    - For example, analytical workloads will typically need to cater to a large read throughput.
  - *What is the required throughput? And how is throughput expected to grow?*
  - *What are the data latency requirements at P50/P99 under reference load levels?*

- Capabilities such as supporting a lock-free design, index tuning, and consistency policies are critical to achieving high-throughput.
  - Optimizing configuration for high throughput incurs trade-offs, which should be fully understood.
  - Load-levelling persistence and messaging patterns, such as CQRS and Event Sourcing, can be used to further optimize throughput.

- Load levels will naturally fluctuate for many application scenarios, with natural peaks requiring a sufficient degree of elasticity to handle variable demand while maintaining throughput and latency.
  - Agile scalability is key to effectively support variable throughput and load levels without overprovisioning capacity levels.
    - Both read and write throughput must scale according to application requirements and load.
    - Both vertical and horizontal scale operations can be applied to respond to changing load levels.

- The impact of throughput dips can vary based on workload scenario.
  - *Will there be connectivity disruption?*
  - *Will individual operations return failure codes while the control plane continues to operate?*
  - *Will the data platform activate throttling, and if so for how long?*

- The fundamental application design recommendation to use an active-active geographical distribution introduces challenges around data consistency.
  - There's a trade-off between consistency and performance with regard to full ACID transactional semantics and traditional locking behavior.
    - Minimizing write latency will come at the cost of data consistency.

- In a multi-region write configuration, changes will need to be synchronized and merged between all replicas, with conflict resolution where required, and this may impact performance levels and scalability.

- Read-only replicas (intra-region and inter-region) can be used to minimize roundtrip latency and distributing traffic to boost performance, throughput, availability, and scalability.

- A caching layer can be used to increase read throughput to improve user experience and end-to-end client response times.
  - Cache expiration times and policies need to be considered to optimize data recentness.

**Data model and query shape**

- The data model, data types, data relationships, and intended query model will strongly affect data platform decisions.
  - *Does the application require a relational data model or can it support a variable-schema or non-relational data model?*
  - *How will the application query data? And will queries depend on database-layer concepts such as relational joins? Or does the application provide such semantics?*

- The nature of datasets considered by the application can be varied, from unstructured content such as images and videos, or more structured files such as CSV and Parquet.
  - Composite application workloads will typically have distinct datasets and associated requirements.

- In addition to relational or non-relational data platforms, graph or key-value data platforms may also be suitable for certain data workloads.
  - Some technologies cater to variable-schema data models, where data items are semantically similar and/or stored and queried together but differ structurally.

- In a microservice architecture, individual application services can be built with distinct scenario-optimized datastores rather than depending on a single monolithic datastore.
  - Design patterns such as [SAGA](/azure/architecture/reference-architectures/saga/saga) can be applied to manage consistency and dependencies between different datastores.
    - Inter-database direct queries can impose co-location constraints.
  - The use of multiple data technologies will add a degree of management overhead to maintain encompassed technologies.

- The feature-sets for each Azure service differ across languages, SDKs, and APIs, which can greatly impact the level of configuration tuning that can be applied.

- Capabilities for optimized alignment with the data model and encompassed data types will strongly influence data platform decisions.
  - Query layers such as stored procedures and object-relational mappers.
  - Language-neutral query capability, such as a secured REST API layer.
  - Business continuity capabilities, such as backup and restore.

- Analytical datastores typically support polyglot storage for various types of data structures.
  - Analytical runtime environments, such as Apache Spark, may have integration restrictions to analyze polyglot data structures.

- In an enterprise context, the use of existing processes and tooling, and the continuity of skills, can have a significant bearing on the data platform design and selection of data technologies.

**Consistency and governance**

- Several factors must be considered to validate the accuracy of data within an application, and the management of these factors can have a significant bearing on the design of the data platform.
  - Data consistency.
  - Platform security features.
  - Data governance.
  - Change management and schema evolution.
  - Dependencies between datasets.

- In any distributed application with multiple data replicas there's a trade-off between consistency and latency, as expressed in the [CAP](https://en.wikipedia.org/wiki/CAP_theorem) and [PACELC](https://en.wikipedia.org/wiki/PACELC_theorem) theorems.
  - When readers and writers are distinctly distributed, an application must choose to return either the fastest-available version of a data item, even if it out of date compared to a just-completed write (update) of that data item in another replica, or the most up-to-date version of the data item, which may incur additional latency to determine and obtain the latest state.
  - Consistency and availability can be configured at platform level or at individual data request level.
  - *What is the user experience if data was to be served from a replica closest to the user which doesn't reflect the most recent state of a different replica? i.e. can the application support possibly serving out-of-date data?*

- In a multi-region write context, when the same data item is changed in two separate write-replicas before either change can be replicated, a conflict is created which must be resolved.
  - Standardized conflict resolution policies, such as "Last Write Wins", or a custom strategy with custom logic can be applied.

- The implementation of security requirements may adversely impact throughput or performance.

- Encryption at-rest can be implemented in the application layer using client-side encryption and/or the data layer using server-side encryption if necessary.

- Azure supports various [encryption models](/azure/security/fundamentals/encryption-overview), including server-side encryption that uses service-managed keys, customer-managed keys in Key Vault, or customer-managed keys on customer-controlled hardware.
  - With client-side encryption, keys can be managed in Key Vault or another secure location.

- MACsec (IEEE 802.1AE MAC) data-link encryption is used to secure all traffic moving between Azure datacenters on the Microsoft backbone network.
  - Packets are encrypted and decrypted on the devices before being sent, preventing physical 'man-in-the-middle' or snooping/wiretapping attacks.

- Authentication and authorization to the data plane and control plane.
  - *How will the data platform authenticate and authorize application access and operational access?*

- Observability through monitoring platform health and data access.
  - *How will alerting be applied for conditions outside acceptable operational boundaries?*

### Design Recommendations

**Capacity and growth**

- Ensure future data volumes associated with organic growth aren't expected to exceed data platform capabilities.
  - Forecast data growth rates aligned to business plans and use established rates to inform ongoing capacity requirements.
  - Compare aggregate and per-data record volumes against data platform limits.
  - If there's a risk of limits being reached in exceptional circumstances, ensure operational mitigations are in place to prevent downtime and data loss.

- Monitor data volume and validate it against a capacity model, considering scale limits and expected data growth rates.
  - Ensure scale operations align with storage, performance, and consistency requirements.
  - When a new scale-unit's introduced, underlying data may need to be replicated which will take time and likely introduce a performance penalty while replication occurs. So ensure these operations are performed outside of critical business hours if possible.

- Define application data tiers to classify datasets based on usage and criticality to facilitate the removal or offloading of older data.
  - Consider classifying datasets into 'hot', 'warm', and 'cold' ('archive') tiers.
    - For example, the foundational reference implementations use Azure Cosmos DB to store 'hot' data that is actively used by the application, while [Azure Blob Storage](../service-guides/azure-blob-storage.md) is used for 'cold' operations data for analytical purposes.

- Configure housekeeping procedures to optimize data growth and drive data efficiencies, such as query performance, and managing data expansion.
  - Configure Time-To-Live (TTL) expiration for data that is no longer required and has no long-term analytical value.
    - Validate that old data can be safely tiered to secondary storage, or deleted outright, without an adverse impact to the application.
  - Offload non-critical data to secondary cold storage, but maintain it for analytical value and to satisfy audit requirements.
  - Collect data platform telemetry and usage statistics to enable DevOps teams to continually evaluate housekeeping requirements and 'right-size' datastores.

- In alignment with a microservice application design, consider the use of multiple different data technologies in parallel, with optimized data solutions for specific workload scenarios and volume requirements.
  - Avoid creating a single monolithic datastore where data volume from expansion can be hard to manage.

**Throughput and latency**

- The data platform must inherently be designed and configured to support high-throughput, with workloads separated into different contexts to maximize performance using scenario optimized data solutions.
  - Ensure read and write throughput for each data scenario can scale according to expected load patterns, with sufficient tolerance for unexpected variance.
  - Separate different data workloads, such as transactional and analytical operations, into distinct performance contexts.

- Load-level through the use of asynchronous non-blocking messaging, for example using the [CQRS](/azure/architecture/patterns/cqrs) or [Event Sourcing](/azure/architecture/patterns/event-sourcing) patterns with [Azure Service Bus](../service-guides/azure-service-bus.md) or [Azure Event Hubs](../service-guides/azure-event-hubs.md).
  - There might be latency between write requests and when new data becomes available to read, which may have an impact on the user experience.
    - This impact must be understood and acceptable in the context of key business requirements.

- Ensure agile scalability to support variable throughput and load levels.
  - If load levels are highly volatile, consider overprovisioning capacity levels to ensure throughput and performance is maintained.
  - Test and validate the impact to composite application workloads when throughput can't be maintained.

- Prioritize Azure-native data services with automated scale-operations to facilitate a swift response to load-level volatility.
  - Configure autoscaling based on service-internal and application-set thresholds.
  - Scaling should initiate and complete in timeframes consistent with business requirements.
  - For scenarios where manual interaction is necessary, establish automated operational 'playbooks' that can be triggered rather than conducting manual operational actions.
    - Consider whether automated triggers can be applied as part of subsequent engineering investments.

- Monitor application data read and write throughput against P50/P99 latency requirements and align to an application capacity model.

- Excess throughput should be gracefully handled by the data platform or application layer and captured by the [health model](../design-guides/health-modeling.md) for operational representation.

- Implement caching for 'hot' data scenarios to minimize response times.
  - Apply appropriate policies for cache expiration and housekeeping to avoid runaway data growth.
    - Expire cache items when the backing data changes.
    - If cache expiration is strictly Time-To-Live (TTL) based, the impact and customer experience of serving outdated data needs to be understood.

**Data model and query shape**

- In alignment with the principle of a cloud- and Azure-native design, it's highly recommended to prioritize managed Azure services to reduce operational and management complexity, and taking advantage of Microsoft's future platform investments.

- In alignment with the application design principle of loosely coupled microservice architectures, allow individual services to use distinct data stores and scenario-optimized data technologies.
  - Identify the types of data structure the application will handle for specific workload scenarios.
  - Avoid creating a dependency on a single monolithic datastore.
    - Consider the [SAGA](/azure/architecture/reference-architectures/saga/saga) design pattern where dependencies between datastores exist.

- Validate that required capabilities are available for selected data technologies.
  - Ensure support for required languages and SDK capabilities. Not every capability is available for every language/SDK in the same fashion.

**Consistency and governance**

- Adopt a multi-region data platform design that supports the workload's active/active deployment approach. For details, see [Global distribution](mission-critical-application-design.md#global-distribution).
  - Distribute data replicas across Availability Zones (AZs) within a region (or use zone-redundant service tiers) to maximize intra-region availability.

- Where consistency requirements allow for it, use a multi-region write data platform design to maximize overall global availability and reliability.
  - Consider business requirements for conflict resolution when the same data item is changed in two separate write replicas before either change can be replicated and thus creating a conflict.
    - Use standardized conflict resolution policies such as "Last one wins" where possible
      - If a custom strategy with custom logic is required, ensure CI/CD DevOps practices are applied to manage custom logic.

- Test and validate backup and restore capabilities, and failover operations through chaos testing within continuous delivery processes.

- Run performance benchmarks to ensure throughput and performance requirements aren't impacted by the inclusion of required security capabilities, such as encryption.
  - Ensure continuous delivery processes consider load testing against known performance benchmarks.

- Prefer service-managed encryption keys unless customer-managed keys are required. For details, see [Data integrity protection](mission-critical-security.md#data-integrity-protection).

> [!NOTE]
> When integrating with a broader organizational implementation, it's critical that an application centric approach be applied for the provisioning and operation of data platform components in an application design.
>
> More specifically, to maximize reliability it's critical that individual data platform components appropriately respond to application health through operational actions which may include other application components. For example, in a scenario where additional data platform resources are needed, scaling the data platform along with other application components according to a capacity model will likely be required, potentially through the provision of additional scale units. This approach will ultimately be constrained if there's a hard dependency of a centralized operations team to address issues related to the data platform in isolation.
>
> Ultimately, the use of centralized data services (that is Central IT DBaaS) can introduce operational bottlenecks and should be avoided in a mission-critical or business-critical context.

### Additional references

Additional data-platform guidance is available within the Azure Application Architecture Guide.

- [Azure Data Store Decision Tree](/azure/architecture/guide/technology-choices/data-store-decision-tree)
- [Criteria for choosing a Data Store](/azure/architecture/guide/technology-choices/data-store-considerations)
- [Non-Relational Data Stores](/azure/architecture/data-guide/big-data/non-relational-data)
- [Relational OLTP Data Stores](/azure/architecture/data-guide/relational-data/online-transaction-processing)

## Globally distributed multi-region write datastore

> [!NOTE]
> For detailed Azure Cosmos DB configuration guidance, see the [Cosmos DB service guide](../service-guides/cosmos-db.md).

To fully accommodate the globally distributed active-active aspirations of an application design, it's strongly recommended to consider a distributed multi-region write data platform, where changes to separate writeable replicas are synchronized and merged between all replicas, with conflict resolution where required.

>[!IMPORTANT]
> The microservices may not all require a distributed multi-region write datastore, so consideration should be given to the architectural context and business requirements of each workload scenario.

Azure Cosmos DB provides a globally distributed and highly available NoSQL datastore, offering multi-region writes and tunable consistency out-of-the-box. The design considerations and recommendations within this section will therefore focus on optimal Azure Cosmos DB usage.

### Design considerations

**Azure Cosmos DB**

Azure Cosmos DB is the recommended primary datastore for mission-critical workloads that require a globally distributed, multi-region write data platform. It provides multi-region writes and tunable consistency out-of-the-box, a 99.999% read/write availability SLA when configured with multiple writable regions, automatic failover, and availability zone redundancy. These capabilities directly support the active/active deployment model that mission-critical workloads require. For the complete feature set, configuration guidance, and best practices, see the [Cosmos DB service guide](../service-guides/cosmos-db.md).

Key mission-critical considerations:

- The multi-region write configuration comes at a significant cost because RU/s are provisioned and billed per region. Two write regions cost 2x, three cost 3x, and so on. Prioritize multi-region writes only for workload scenarios that require maximum reliability.
- In a multi-region write configuration, [update conflicts](/azure/cosmos-db/conflict-resolution-policies) can occur. Azure Cosmos DB provides Last Write Wins (default) and custom conflict resolution policies.
- The data model and partitioning strategy across logical and physical partitions plays a critical role in achieving optimal performance and availability.
- Autoscale throughput protects against throttling errors by scaling automatically, which is important for unpredictable mission-critical workloads.
- Continuous backup mode allows self-service point-in-time restore (PITR) with one-second granularity and up to 30 days retention, which is preferred over periodic backups for mission-critical scenarios.
  - [Azure Data Factory Connector for Azure Cosmos DB](/azure/data-factory/connector-azure-cosmos-db) ([Azure Cosmos DB for NoSQL](/azure/data-factory/connector-azure-cosmos-db) or [MongoDB API](/azure/data-factory/connector-azure-cosmos-db-mongodb-api) connectors) to copy data.
    - Azure Data Factory (ADF) supports manual execution and [Schedule](/azure/data-factory/concepts-pipeline-execution-triggers#schedule-trigger), [Tumbling window](/azure/data-factory/concepts-pipeline-execution-triggers#tumbling-window-trigger), and [Event-based](/azure/data-factory/concepts-pipeline-execution-triggers#event-based-trigger) triggers.
      - Provides support for both Storage and [Event Grid](../service-guides/azure-event-grid.md).
    - ADF is primarily suitable for periodic custom backup implementations due to its batch-oriented orchestration.
      - It's less suitable for continuous backup implementations with frequent events due to the orchestration execution overhead.
    - ADF supports [Azure Private Link](/azure/data-factory/data-factory-private-link) for high network security scenarios

### Design Recommendations

**Azure Cosmos DB**

- Use Azure Cosmos DB as the primary data platform where requirements allow.

- For mission-critical workload scenarios, configure Azure Cosmos DB with a write replica inside each deployment region to reduce latency and provide maximum redundancy.
  - Configure the application to [prioritize the use of the local Azure Cosmos DB replica](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) for writes and reads to optimize application load, performance, and regional RU/s consumption.
  - The multi-region-write configuration comes at a significant cost and should be prioritized only for workload scenarios requiring maximum reliability.

- For less-critical workload scenarios, prioritize the use of single-region-write configuration (when using Availability Zones) with globally distributed read replicas.
  - Configure the application to use the local Azure Cosmos DB read replica to optimize read performance.

- Select an optimal 'hub' deployment region where conflict resolution will occur in a multi-region-write configuration, and all writes will be performed in a single-region-write configuration.
  - Consider distance relative to other deployment regions and associated latency in selecting a primary region, and requisite capabilities such as Availability Zones support.

- Configure Azure Cosmos DB with [Availability Zone (AZ) redundancy](/azure/cosmos-db/high-availability#availability-zone-support) in all deployment regions with AZ support, to ensure resiliency to zone failures within a region.

- Use Azure Cosmos DB for NoSQL since it offers the most comprehensive feature set, particularly where performance tuning is concerned.
  - Alternative APIs should primarily be considered for migration or compatibility scenarios.
    - When using alternative APIs, validate that required capabilities are available with the selected language and SDK to ensure optimal configuration and performance.

- Use the Direct connection mode to optimize network performance through direct TCP connectivity to backend Azure Cosmos DB nodes, with a reduced number of network 'hops'.

> The Azure Cosmos DB SLA is calculated by averaging failed requests, which may not directly align with a 99.999% reliability tier error budget. When designing for 99.999% SLO, it's therefore vital to plan for regional and multi-region Azure Cosmos DB write unavailability, ensuring a fallback storage technology is positioned if a failure, such as a persisted message queue for subsequent replay.

- Define a partitioning strategy across both logical and physical partitions to optimize data distribution according to the data model.
  - Minimize cross-partition queries.
  - Iteratively [test and validate](/azure/cosmos-db/how-to-model-partition-example) the partitioning strategy to ensure optimal performance.

- [Select an optimal partition key](/azure/cosmos-db/partitioning#choose-a-partition-key).
  - The partition key can't be changed after it has been created within the container.
  - The partition key should be a property value that doesn't change.
  - Select a partition key that has a high cardinality, with a wide range of possible values.
  - The partition key should spread RU consumption and data storage evenly across all logical partitions to ensure even RU consumption and storage distribution across physical partitions.
  - Run read queries against the partitioned column to reduce RU consumption and latency.

- [Indexing](/azure/cosmos-db/index-overview) is also crucial for performance, so ensure index exclusions are used to reduce RU/s and storage requirements.
  - Only index those fields that are needed for filtering within queries; design indexes for the most-used predicates.

- Leverage the built-in error handling, retry, and broader reliability capabilities of the [Azure Cosmos DB SDK](/azure/cosmos-db/best-practice-dotnet#checklist).
  - Implement [retry logic](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications) within the SDK on clients.

- **Use service-managed encryption keys** to reduce management complexity. If there's a specific security requirement for customer-managed keys, use [Azure Key Vault Managed HSM](/azure/key-vault/managed-hsm/overview) with FIPS 140 Level 3 compliance.
  - Ensure appropriate key management procedures are applied, such as backup and rotation.

- Disable [Azure Cosmos DB key-based metadata write access](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4750c32b-89c0-46af-bfcb-2e4541a818d5) by applying the built-in Azure Policy.

- Enable [Azure Monitor](/azure/cosmos-db/monitor-cosmos-db) to gather key metrics and diagnostic logs, such as provisioned throughput (RU/s).
  - Route Azure Monitor operational data into a [Log Analytics workspace](../service-guides/azure-log-analytics.md) dedicated to Azure Cosmos DB and other global resources within the application design.

- Evaluate application traffic patterns to select an optimal option for [provisioned throughput types](/azure/cosmos-db/how-to-choose-offer).
  - Consider auto-scale provisioned throughput to automatically level-out workload demand.

- When using [AKS](../service-guides/azure-kubernetes-service.md) as the compute platform: For query-intensive workloads, select an AKS node SKU that has accelerated networking enabled to reduce latency and CPU jitters.

- For single write region deployments, it's strongly recommended to configure Azure Cosmos DB for [service-managed failover](/azure/reliability/reliability-cosmos-db-nosql#service-managed-failover).

- Load-level through the use of asynchronous non-blocking messaging within system flows, which write updates to Azure Cosmos DB.
  - Consider patterns such as [Command and Query Responsibility Segregation](/azure/architecture/patterns/cqrs) and [Event Sourcing](/azure/architecture/patterns/event-sourcing) with [Azure Service Bus](../service-guides/azure-service-bus.md) or [Azure Event Hubs](../service-guides/azure-event-hubs.md).

- It's strongly recommended to practice recovery procedures on non-production resources and data, as part of standard business continuity operation preparation.

- Define IaC artifacts to re-establish configuration settings and capabilities of an Azure Cosmos DB backup restore.

- Evaluate and apply the [Azure Security Baseline](/security/benchmark/azure/baselines/cosmos-db-security-baseline#backup-and-recovery) control guidance for Azure Cosmos DB Backup and Recovery.
  - [BR-1: Ensure regular automated backups](/security/benchmark/azure/baselines/cosmos-db-security-baseline#br-1-ensure-regular-automated-backups)
  - [BR-3: Monitor backups](/security/benchmark/azure/security-controls-v3-backup-recovery#br-3-monitor-backups)
  - [BR-4: Regularly test backup](/security/benchmark/azure/security-controls-v3-backup-recovery#br-4-regularly-test-backup)

- For new zero-ETL analytics projects, use [Azure Cosmos DB Mirroring for Microsoft Fabric](/fabric/database/mirrored-database/azure-cosmos-db).

## Relational data technologies

> [!NOTE]
> For detailed Azure SQL Database configuration guidance, see the [SQL Database service guide](../service-guides/azure-sql-database.md).

> [!NOTE]
> For detailed Azure Database for PostgreSQL configuration guidance, see the [Database for PostgreSQL service guide](../service-guides/postgresql.md).

For scenarios with a highly relational data model or dependencies on existing relational technologies, the use of Azure Cosmos DB in a multi-region write configuration might not be directly applicable. In such cases, it's vital that used relational technologies are designed and configured to uphold the multi-region active-active aspirations of an application design.

Mission-critical workloads benefit from polyglot persistence: use the best data store for each workload scenario rather than forcing all data through a single technology. Azure Cosmos DB handles globally distributed writes, while relational databases like Azure SQL Database and Azure Database for PostgreSQL serve scenarios with strongly relational models, complex transactions, or regulatory constraints that require ACID guarantees.

### Design considerations

- Relational data technologies can scale read operations easily, but writes are typically constrained to a single primary instance, which limits scalability and performance for globally distributed workloads.

- [Sharding](/azure/sql-database/sql-database-elastic-scale-introduction) can distribute data and processing across multiple databases to navigate platform constraints. This is particularly relevant when the application design considers three or more Azure regions.

- Azure SQL Database provides auto-failover groups, geo-replication across up to four regions, availability zone redundancy, and a 99.995% SLA on Business Critical tiers. These capabilities make it suitable for mission-critical scenarios where relational requirements exist. For complete capabilities, see the [SQL Database service guide](../service-guides/azure-sql-database.md).

- Azure Database for PostgreSQL Flexible Server with [Elastic Clusters (Citus)](/azure/postgresql/flexible-server/concepts-elastic-clusters) provides dynamic scalability through sharding. Use Flexible Server for mission-critical workloads that require availability zone support and the 99.95% SLA guarantee. For complete capabilities, see the [Database for PostgreSQL service guide](../service-guides/postgresql.md).

### Design recommendations

- Consider sharding to partition relational databases based on different application and data contexts, helping navigate platform constraints, maximize scalability and availability, and fault isolation.
  - Sharding isn't appropriate for all application scenarios, so a contextualized evaluation is required.

- Prioritize the use of Azure SQL Database where relational requirements exist due to its maturity on the Azure platform and wide array of reliability capabilities.

- Use Auto Failover Groups to provide [transparent failover](/azure/azure-sql/database/designing-cloud-solutions-for-disaster-recovery) to a secondary region. Consider automated operational triggers, based on alerting aligned to the [application health model](../design-guides/health-modeling.md#set-actionable-alerts), to conduct failovers if a failure impacts the primary and secondary.

>[!IMPORTANT]
> For applications considering more than four deployment regions, serious consideration should be given to application-scoped sharding or refactoring the application to support multi-region write technologies, such as Azure Cosmos DB.

## Caching for Hot Tier Data

An in-memory caching layer can be applied to enhance a data platform by significantly increasing read throughput and improving end-to-end client response times for hot tier data scenarios.

Azure provides several services with applicable capabilities for caching key data structures. This section focuses on scenarios where additional read performance and data access durability is required.

### Design Considerations

- A caching layer provides additional data access durability since even if an outage impacting the underlying data technologies, an application data snapshot can still be accessed through the caching layer.

- However, a caching layer is also another point of failure for mission-critical workloads. Evaluate whether the read performance benefit outweighs the added operational complexity and failure surface.

- In certain workload scenarios, in-memory caching can be implemented within the application platform itself.

**Azure Managed Redis and Azure Cache for Redis**

- Azure Cache for Redis is retiring. For new designs, use [Azure Managed Redis](/azure/redis/overview).

- With geo-replication, charges for data transfer between regions will also be applicable in addition to the direct costs associated with cache instances.

### Design Recommendations

- Consider an optimized caching layer for 'hot' data scenarios to increase read throughput and improve response times.

- Apply appropriate policies for cache expiration and housekeeping to avoid runaway data growth.
  - Consider expiring cache items when the backing data changes.

**Azure Managed Redis and Azure Cache for Redis**

- Use [Azure Managed Redis](/azure/redis/overview) for new cache deployments.
- For existing Azure Cache for Redis instances, follow the [retirement guidance](/azure/azure-cache-for-redis/retirement-faq).

- Deploy replica instances using geo-replication in an active configuration across all considered deployment regions.

- Ensure replica instances are deployed across Availability Zones within each considered Azure region.

- Use Azure Monitor to evaluate cache instances.
  - Calculate a [health score](../design-guides/health-modeling.md) for regional cache components to observe health relative to business requirements and resource utilization.
  - Observe and alert on key metrics such as high CPU, high memory usage, high server load, and evicted keys for insights when to scale the cache.

## Analytical Scenarios

It's increasingly common for mission-critical applications to consider analytical scenarios as a means to drive additional value from encompassed data flows. Application and operational (AIOps) analytical scenarios therefore form a crucial aspect of highly reliable data platform.

Analytical and transactional workloads require different data platform capabilities and optimizations for acceptable performance within their respective contexts.

| Description | Analytical | Transactional |
| --- | --- | --- |
| Use Case | Analyze very large volumes of data ("big data") | Process very large volumes of individual transactions |
| Optimized for | Read queries and aggregations over many records | Near real-time Create/Read/Update/Delete (CRUD) queries over few records |
| Key Characteristics | - Consolidate from data sources of record<br />- Column-based storage<br />- Distributed storage<br />- Parallel processing<br />- Denormalized<br />- Low concurrency reads and writes<br />- Optimize for storage volume with compression | - Data source of record for application<br />- Row-based Storage<br />- Contiguous storage<br />- Symmetrical processing<br />- Normalized<br />- High concurrency reads and writes, index updates<br />- Optimize for fast data access with in-memory storage |

Current Azure Cosmos DB guidance recommends [Azure Cosmos DB Mirroring for Microsoft Fabric](/fabric/database/mirrored-database/azure-cosmos-db) for new zero-ETL analytics projects instead of Synapse Link.

### Design Considerations

- Traditionally, large-scale analytical scenarios are facilitated by extracting data into a separate data platform optimized for subsequent analytical queries.
  - Extract, Transform, and Load (ETL) pipelines consume throughput and can impact transactional workload performance.
  - Running ETL pipelines infrequently to reduce throughput and performance impacts will result in analytical data that is less up-to-date.
  - ETL pipeline development and maintenance overhead increases as data transformations become more complex.
    - For example, if source data is frequently changed or deleted, ETL pipelines must account for those changes in the target data for analytical queries through an additive/versioned approach, dump and reload, or in-place changes on the analytical data. Each of these approaches will have derivative impact, such as index re-creation or update.

**Azure Cosmos DB**

- Analytical queries run on Azure Cosmos DB transactional data will typically aggregate across partitions over large volumes of data, consuming significant Request Unit (RU) throughput, which can impact the performance of surrounding transactional workloads.

- The [Azure Cosmos DB change feed](/azure/cosmos-db/change-feed) can also be used to maintain a separate secondary data store for analytical scenarios.

### Design Recommendations

- Ensure analytical workloads don't impact transactional application workloads to maintain transactional performance.

- Use [Azure Cosmos DB Mirroring for Microsoft Fabric](/fabric/database/mirrored-database/azure-cosmos-db) for new zero-ETL analytics projects.

- Use the Azure Cosmos DB change feed only for simple analytical scenarios.

## Next step

Review the considerations for networking considerations.

> [!div class="nextstepaction"]
> [Network and connectivity](mission-critical-networking-connectivity.md)
