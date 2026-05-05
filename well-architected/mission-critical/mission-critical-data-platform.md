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

- Excess throughput should be gracefully handled by the data platform or application layer and captured by the [health model](mission-critical-health-modeling.md) for operational representation.

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

- Azure Cosmos DB stores data within Containers, which are indexed, row-based transactional stores designed to allow fast transactional reads and writes with response times on the order of milliseconds.

- Azure Cosmos DB supports multiple different APIs with differing feature sets, such as NoSQL, Apache Cassandra, and MongoDB.
  - The first-party Azure Cosmos DB for NoSQL provides the richest feature set and is typically the API where new capabilities will become available first.

- Azure Cosmos DB supports [Gateway and Direct connectivity modes](/azure/cosmos-db/sdk-connection-modes), where Direct facilitates connectivity over TCP to backend Azure Cosmos DB replica nodes for improved performance with fewer network hops, while Gateway provides HTTPS connectivity to frontend gateway nodes.
  - Direct mode is only available when using the Azure Cosmos DB for NoSQL and is currently only supported on .NET and Java SDK platforms.

- Within Availability Zone enabled regions, Azure Cosmos DB offers [Availability Zone (AZ) redundancy](/azure/cosmos-db/high-availability#availability-zone-support) support for high availability and resiliency to zone failures within a region.

- Azure Cosmos DB maintains four replicas of data within a single region, and when Availability Zone (AZ) redundancy is enabled, Azure Cosmos DB ensures data replicas are placed across multiple AZs to protect against zone failures.
  - The Paxos consensus protocol is applied to achieve quorum across replicas within a region.

- An Azure Cosmos DB account can easily be configured to replicate data across multiple regions to mitigate the risk of a single region becoming unavailable.
  - Replication can be configured with either single-region writes or multi-region writes.
    - With single region writes, a primary 'hub' region is used to serve all writes and if this 'hub' region becomes unavailable, a failover operation must occur to promote another region as writable.
    - With multi-region writes, applications can write to any configured deployment region, which will replicate changes between all other regions. If a region is unavailable then the remaining regions will be used to serve write traffic.

- In a multi-region write configuration, [update (insert, replace, delete) conflicts](/azure/cosmos-db/conflict-resolution-policies) can occur where writers concurrently update the same item in multiple regions.

- Azure Cosmos DB provides two conflict resolution policies, which can be applied to automatically address conflicts.
  - Last Write Wins (LWW) applies a time-synchronization clock protocol using a system-defined timestamp `_ts` property as the conflict resolution path. If of a conflict the item with the highest value for the conflict resolution path becomes the winner, and if multiple items have the same numeric value then the system selects a winner so that all regions can converge to the same version of the committed item.
    - With delete conflicts, the deleted version always wins over either insert or replace conflicts regardless of conflict resolution path value.
    - Last Write Wins is the default conflict resolution policy.
    - When using Azure Cosmos DB for NoSQL, a custom numerical property such as a custom timestamp definition can be used for conflict resolution.
  - Custom resolution policies allow for application-defined semantics to reconcile conflicts using a registered merge stored procedure that is automatically invoked when conflicts are detected.
    - The system provides exactly once guarantee for the execution of a merge procedure as part of the commitment protocol.
    - A custom conflict resolution policy is only available with Azure Cosmos DB for NoSQL and can only be set at container creation time.

- In a multi-region write configuration, there's a dependency on a single Azure Cosmos DB 'hub' region to perform all conflict resolutions, with the Paxos consensus protocol applied to achieve quorum across replicas within the hub region.
  - The platform provides a message buffer for write conflicts within the hub region to load level and provide redundancy for transient faults.
    - The buffer is capable of storing a few minutes worth of write updates requiring consensus.

- The primary 'hub' region is determined by the first region that Azure Cosmos DB is configured within.
  - A priority ordering is configured for additional satellite deployment regions for failover purposes.

- The data model and partitioning across logical and physical partitions plays an important role in achieving optimal performance and availability.

- When deployed with a single write region, Azure Cosmos DB can be configured for [service-managed failover](/azure/reliability/reliability-cosmos-db-nosql#service-managed-failover) based on a defined failover priority considering all read region replicas.

- Azure Cosmos DB offers a [99.999% SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) for both read and write availability for Database Accounts configured with multiple Azure regions as writable.
  - The SLA is represented by the Monthly Uptime Percentage, which is calculated as 100% - Average Error Rate.
  - The Average Error Rate is defined as the sum of Error Rates for each hour in the billing month divided by the total number of hours in the billing month, where the Error Rate is the total number of Failed Requests divided by Total Requests during a given one-hour interval.

- Azure Cosmos DB offers a 99.99% SLA for throughput, consistency, availability, and latency for Database Accounts scoped to a single Azure region when configured with any of the five Consistency Levels.
  - A 99.99% SLA also applies to Database Accounts spanning multiple Azure regions configured with any of the four relaxed Consistency Levels.

- There are two types of throughput that can be provisioned in Azure Cosmos DB, standard and [autoscale](/azure/cosmos-db/provision-throughput-autoscale), which are measured using Request Units per second (RU/s).
  - Standard throughput allocates resources required to guarantee a specified RU/s value.
    - Standard is billed hourly for provisioned throughput.
  - Autoscale defines a maximum throughput value, and Azure Cosmos DB will automatically scale up or down depending on application load, between the maximum throughput value and a minimum of 10% of the maximum throughput value.
    - Autoscale is billed hourly for the maximum throughput consumed.

- Static provisioned throughput with a variable workload may result in throttling errors, which will impact perceived application availability.
  - Autoscale protects against throttling errors by enabling Azure Cosmos DB to scale up as needed, while maintaining cost protection by scaling back down when load decreases.

- When Azure Cosmos DB is replicated across multiple regions, the provisioned Request Units (RUs) are billed per region.

- There's a significant cost delta between a multi-region-write and single-region-write configuration which in many cases may make a multi-master Azure Cosmos DB data platform cost prohibitive.

| Single Region Read/Write | Single Region Write - Dual Region Read | Dual Region Read/Write |
|---|---|---|
| 1 RU | 2 RU | 4 RU |

> The delta between single-region-write and multi-region-write is actually less than the 1:2 ratio reflected in the table above. More specifically, there's a cross-region data transfer charge associated with write updates in a single-write configuration, which isn't captured within the RU costs as with the multi-region write configuration.  

- Consumed storage is billed as a flat rate for the total amount of storage (GB) consumed to host data and indexes for a given hour.

- `Session` is the default and most widely used [consistency level](/azure/cosmos-db/consistency-levels) since data is received in the same order as writes.

- Azure Cosmos DB supports authentication via either a Microsoft Entra identity or Azure Cosmos DB keys and resource tokens, which provide overlapping capabilities.

![Azure Cosmos DB Access Capabilities](/azure/cosmos-db/media/how-to-restrict-user-data/operations.png "Azure Cosmos DB Access Capabilities")

- It's possible to disable resource management operations using keys or resource tokens to limit keys and resource tokens to data operations only, allowing for fine-grained resource access control using Microsoft Entra role-based access control (RBAC).
  - Restricting control plane access via keys or resource tokens will disable control plane operations for clients using Azure Cosmos DB SDKs and should therefore be thoroughly [evaluated and tested](/azure/cosmos-db/how-to-connect-role-based-access-control#validate-that-key-based-authentication-is-disabled).
  - The `disableKeyBasedMetadataWriteAccess` setting can be configured via [ARM Template](/azure/cosmos-db/how-to-connect-role-based-access-control#disable-key-based-authentication) IaC definitions, or via a [Built-In Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4750c32b-89c0-46af-bfcb-2e4541a818d5).

- Azure Cosmos DB supports control plane and data plane role-based access control with Microsoft Entra ID.
  - Application administrators can create role assignments for users, groups, or managed identities to grant or deny access to resources and operations on Azure Cosmos DB resources.
  - For control plane operations, there are several [Built-in RBAC Roles](/azure/cosmos-db/how-to-connect-role-based-access-control#grant-control-plane-role-based-access) available for role assignment, and [custom RBAC roles](/azure/cosmos-db/how-to-connect-role-based-access-control#grant-control-plane-role-based-access) can also be used to form specific [privilege combinations](/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb).
    - [Cosmos DB Account Reader](/azure/role-based-access-control/built-in-roles#cosmos-db-account-reader-role) enables read-only access to the Azure Cosmos DB resource.
    - [DocumentDB Account Contributor](/azure/role-based-access-control/built-in-roles#documentdb-account-contributor) enables management of Azure Cosmos DB accounts including keys and role assignments, but doesn't enable data-plane access.
    - [Cosmos DB Operator](/azure/role-based-access-control/built-in-roles#cosmos-db-operator), which is similar to DocumentDB Account Contributor, but doesn't provide the ability to manage keys or role assignments.

- Azure Cosmos DB resources (accounts, databases, and containers) can be protected against incorrect modification or deletion using [Resource Locks](/azure/cosmos-db/resource-locks).
  - Resource Locks can be set at the account, database, or container level.
  - A Resource Lock set at on a resource will be inherited by all child resources. For example, a Resource Lock set on the Azure Cosmos DB account will be inherited by all databases and containers within the account.
  - Resource Locks **only** apply to control plane operations and do **not** prevent data plane operations, such as creating, changing, or deleting data.
  - If control plane access isn't restricted with `disableKeyBasedMetadataWriteAccess`, then clients will be able to perform control plane operations using account keys.

- The [Azure Cosmos DB change feed](/azure/cosmos-db/change-feed) provides a time-ordered feed of changes to data in an Azure Cosmos DB container.
  - Latest version mode includes insert and update operations; [all versions and deletes mode](/azure/cosmos-db/change-feed-modes#all-versions-and-deletes-change-feed-mode-preview) can include deletes and TTL expirations.

- The change feed can be used to maintain a separate data store from the primary Container used by the application, with ongoing updates to the target data store fed by the change feed from the source Container.
  - The change feed can be used to populate a secondary store for additional data platform redundancy or for subsequent analytical scenarios.

- If you use latest version mode and delete operations routinely affect the data within the source Container, then the store fed by the change feed will be inaccurate and unreflective of deleted data.
  - A [Soft Delete](/azure/cosmos-db/change-feed-design-patterns#deletes) pattern can be implemented so that data records are included in the change feed.
    - Instead of explicitly deleting data records, data records are *updated* by setting a flag (e.g. `IsDeleted`) to indicate that the item is considered deleted.
    - Any target data store fed by the change feed will need to detect and process items with a deleted flag set to True; instead of storing soft-deleted data records, the *existing* version of the data record in the target store will need to be deleted.
  - A short Time-To-Live (TTL) is typically used with the soft-delete pattern so that Azure Cosmos DB automatically deletes expired data, but only after it's reflected within the change feed with the deleted flag set to True.
    - This preserves the original delete intent while also propagating the delete through the change feed.

- Azure Cosmos DB can be configured as an [analytical store](/azure/cosmos-db/analytical-store-introduction), which applies a column format for optimized analytical queries to address the complexity and latency challenges that occur with the traditional ETL pipelines.

- Azure Cosmos DB automatically backs up data at regular intervals without affecting the performance or availability, and without consuming RU/s.

- Azure Cosmos DB can be configured according to two distinct backup modes.
  - [Periodic](/azure/cosmos-db/configure-periodic-backup-restore) is the default backup mode for all accounts, where backups are taken at a periodic interval and the data is restored by creating a request with the support team.
    - The default periodic backup retention period is eight hours and the default backup interval is four hours, which means only the latest two backups are stored by default.
    - The backup interval and retention period are configurable within the account.
      - The maximum retention period extends to a month with a minimum backup interval of one hour.
      - A role assignment to the Azure "Cosmos DB Account Reader Role" is required to configure backup storage redundancy.
    - Two backup copies are included at no extra cost, but additional backups incur additional costs.
    - By default, periodic backups are stored within separate Geo-Redundant Storage (GRS) that isn't directly accessible.
      - Backup storage exists within the primary 'hub' region and is replicated to the paired region through underlying storage replication.
      - The redundancy configuration of the underlying backup storage account is configurable to [Zone-Redundant Storage or Locally-Redundant Storage](/azure/cosmos-db/periodic-backup-restore-introduction#how-azure-cosmos-db-performs-data-backup).
    - Performing a **restore operation requires a [Support Request](/azure/cosmos-db/periodic-backup-request-data-restore)** since customers can't directly perform a restore.
      - Before opening a support ticket, the backup retention period should be increased to at least seven days within eight hours of the data loss event.
    - A restore operation creates a new Azure Cosmos DB account where data is recovered.
      - An existing Azure Cosmos DB account can't be used for Restore
      - By default, a new Azure Cosmos DB account named `<Azure_Cosmos_account_original_name>-restored<n>` will be used.
        - This name can be adjusted, such as by reusing the existing name if the original account was deleted.
    - If throughput is provisioned at the database level, backup and restore will happen at the database level
      - It's not possible to select a subset of containers to restore.
  - [Continuous](/azure/cosmos-db/continuous-backup-restore-introduction) backup mode allows for a restore to any point of time within a 7-day or 30-day retention window, depending on the selected tier.
    - Restore operations can be performed to return to a specific point in time (PITR) with a one-second granularity.
    - The available window for restore operations is up to 30 days.
      - It's also possible to restore to the resource instantiation state.
    - Continuous backups are taken within every Azure region where the Azure Cosmos DB account exists.
      - Continuous backups are stored within the same Azure region as each Azure Cosmos DB replica, using Locally-Redundant Storage (LRS) or Zone Redundant Storage (ZRS) within regions that support Availability Zones.
    - A self-service restore can be performed using the [Azure portal, Azure CLI, Azure PowerShell, or ARM templates](/azure/cosmos-db/continuous-backup-restore-introduction).
    - There are several [limitations](/azure/cosmos-db/continuous-backup-restore-introduction#current-limitations) with Continuous Backup.
      - If a container has TTL configured, restored data that has exceeded its TTL may be *immediately deleted*
    - A restore operation can restore into a new Azure Cosmos DB account, and some scenarios support restore into an existing account.
    - There's an [additional storage cost](/azure/cosmos-db/continuous-backup-restore-introduction#continuous-backup-pricing) for Continuous backups and restore operations.

- Existing Azure Cosmos DB accounts can be migrated from Periodic to Continuous, but not from Continuous to Periodic; migration is one-way and not reversible.

- Azure Cosmos DB backups don't include all account configuration. Redeploy configuration settings and capabilities after restore.
- It's possible to implement a custom backup and restore capability for scenarios where Periodic and Continuous approaches aren't a good fit.
  - A custom approach introduces significant costs and additional administrative overhead, which should be understood and carefully assessed.
    - Common restore scenarios should be modeled, such as the corruption or deletion of an account, database, container, or data item.
    - Housekeeping procedures should be implemented to prevent backup sprawl.
  - [Azure Blob Storage](../service-guides/azure-blob-storage.md) or an alternative data technology can be used, such as an alternative Azure Cosmos DB container.
    - Azure Blob Storage and Azure Cosmos DB provide native integrations with Azure services such as [Azure Functions](../service-guides/azure-functions.md) and Azure Data Factory.

- Custom backups can use two options.
  - [Azure Cosmos DB change feed](/azure/cosmos-db/change-feed) to write data to a separate storage facility.
    - An [Azure Function](/azure/cosmos-db/change-feed-functions) or equivalent application process uses the [change feed processor](/azure/cosmos-db/change-feed-processor) to bind to the change feed and process items into storage.
  - Both continuous or periodic (batched) custom backups can be implemented using the change feed.
  - The Azure Cosmos DB change feed latest version mode doesn't reflect deletes, so a soft-delete pattern must be applied using a boolean property and TTL.
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
> For detailed Azure Database for PostgreSQL configuration guidance, see the [Database for PostgreSQL service guide](../service-guides/postgresql.md).

> [!NOTE]
> For detailed Azure SQL Database configuration guidance, see the [SQL Database service guide](../service-guides/azure-sql-database.md).

For scenarios with a highly relational data model or dependencies on existing relational technologies, the use of Azure Cosmos DB in a multi-region write configuration might not be directly applicable. In such cases, it's vital that used relational technologies are designed and configured to uphold the multi-region active-active aspirations of an application design.

Azure provides many managed relational data platforms, including Azure SQL Database and Azure Database for common OSS relational solutions, including [MySQL](../service-guides/azure-database-for-mysql.md), PostgreSQL, and MariaDB. The design considerations and recommendations within this section will therefore focus on the optimal usage of Azure SQL Database and Azure Database OSS flavors to maximize reliability and global availability.

### Design considerations

- Relational data technologies can be configured to easily scale read operations, but writes are typically constrained to go through a single primary instance, which places a significant constraint on scalability and performance.

- [Sharding](/azure/sql-database/sql-database-elastic-scale-introduction) can be applied to distribute data and processing across multiple identical structured databases, partitioning databases horizontally to navigate platform constraints.
  - For example, sharding is often applied in multi-tenant SaaS platforms to isolate groups of tenants into distinct data platform constructs.

**Azure SQL Database**

- Azure SQL Database provides a fully managed database engine that is always running on the latest stable version of the SQL Server database engine and underlying Operating System.
  - Provides intelligent features such as performance tuning, threat monitoring, and vulnerability assessments.

- Azure SQL Database provides built-in regional high availability and turnkey geo-replication to distribute read-replicas across Azure regions.
  - With geo-replication, secondary database replicas remain read-only until a failover is initiated.
  - Up to four secondaries are supported in the same or different regions.
  - Secondary replicas can also be used for read-only query access to optimize read performance.
  - Failover must be initiated manually but can be wrapped in automated operational procedures.

- Azure SQL Database provides [Auto Failover Groups](/azure/azure-sql/database/auto-failover-group-overview?tabs=azure-powershell), which replicates databases to a secondary server and allows for transparent failover if a failure.
  - Auto-failover groups support geo-replication of all databases in the group to only one secondary server or instance in a different region.
  - Secondary databases can be used to offload read traffic.

- Premium, Business Critical, General Purpose, and Hyperscale service tier database replicas can be [distributed across Availability Zones](/azure/azure-sql/database/high-availability-sla) where supported.
  - The control ring is also duplicated across multiple zones as three gateway rings (GW).
    - The routing to a specific gateway ring is controlled by Azure Traffic Manager.

- Azure SQL Database offers a baseline 99.99% availability SLA across all of its service tiers, but provides a higher 99.995% SLA for the Business Critical or Premium tiers in regions that support availability zones.
  

- Compute costs associated with Azure SQL Database can be reduced using a [Reservation Discount](/azure/cost-management-billing/reservations/understand-reservation-charges).
  - It's not possible to apply reserved capacity for DTU-based databases.

- [Point-in-time restore](/azure/azure-sql/database/recovery-using-backups#point-in-time-restore) can be used to return a database and contained data to an earlier point in time.

- [Geo-restore](/azure/sql-database/sql-database-recovery-using-backups) can be used to recover a database from a geo-redundant backup.

**Azure Database For PostgreSQL**

- Azure Database for PostgreSQL is offered as [Flexible Server](/azure/postgresql/single-server/whats-happening-to-postgresql-single-server). Single Server is retired, and Hyperscale (Citus) capabilities are now part of Flexible Server.

- [Elastic Clusters (Citus)](/azure/postgresql/flexible-server/concepts-elastic-clusters) provides dynamic scalability through sharding.
  - Distributing table rows across multiple PostgreSQL servers is key to ensure scalable queries in Elastic Clusters (Citus).
  - Multiple nodes can collectively hold more data than a traditional database, and in many cases can use worker CPUs in parallel to optimize costs.

- Flexible server provides cost efficiencies for non-production workloads through the ability to stop/start the server, and a burstable compute tier that is suitable for workloads that don't require continuous compute capacity.

- There's no additional charge for backup storage for up to 100% of total provisioned server storage.
  - Additional consumption of backup storage is charged according to consumed GB/month.

- Compute costs associated with Azure Database for PostgreSQL can be reduced using [Flexible Server Reserved Pricing](/azure/postgresql/flexible-server/concept-reserved-pricing).

### Design Recommendations

- Consider sharding to partition relational databases based on different application and data contexts, helping to navigate platform constraints, maximize scalability and availability, and fault isolation.
  - This recommendation is particularly prevalent when the application design considers three or more Azure regions since relational technology constraints can significantly hinder globally distributed data platforms.
  - Sharding isn't appropriate for all application scenarios, so a contextualized evaluation is required.

- Prioritize the use of Azure SQL Database where relational requirements exist due to its maturity on the Azure platform and wide array of reliability capabilities.

**Azure SQL Database**

- Use the Business-Critical service tier to maximize reliability and availability, including access to critical resiliency capabilities.

- Use the vCore based consumption model to facilitate the independent selection of compute and storage resources, tailored to workload volume and throughput requirements.
  - Ensure a defined capacity model is applied to inform compute and storage resource requirements.
    - Consider [Reserved Capacity](/azure/azure-sql/database/reserved-capacity-overview) to provide potential cost optimizations.

- Configure the Zone-Redundant deployment model to spread Business Critical database replicas within the same region across Availability Zones.

- Use [Active Geo-Replication](/azure/azure-sql/database/active-geo-replication-overview) to deploy readable replicas within all deployment regions (up to four).

- Use Auto Failover Groups to provide [transparent failover](/azure/azure-sql/database/designing-cloud-solutions-for-disaster-recovery) to a secondary region, with geo-replication applied to provide replication to additional deployment regions for read optimization and database redundancy.
  - For application scenarios limited to only two deployment regions, the use of Auto Failover Groups should be prioritized.

- Consider automated operational triggers, based on alerting aligned to the [application health model](mission-critical-health-modeling.md#analyze-and-alert-on-health), to conduct failovers to geo-replicated instances if a failure impacting the primary and secondary within the Auto Failover Group.

>[!IMPORTANT]
> For applications considering more than four deployment regions, serious consideration should be given to application scoped sharding or refactoring the application to support multi-region write technologies, such as Azure Cosmos DB. However, if this isn't feasible within the application workload scenario, it's advised to elevate a region within a single geography to a primary status encompassing a geo-replicated instance to more evenly distributed read access.

  - Ensure CD pipelines consider load testing under representative load levels to validate appropriate data platform behavior.

- Calculate a health metric for database components to observe health relative to business requirements and resource utilization, using  [monitoring and alerts](/azure/azure-sql/database/monitor-tune-overview) to drive automated operational action where appropriate.
  - Ensure key query performance metrics are incorporated so swift action can be taken when service degradation occurs.

- Optimize queries, tables, and databases using [Query Performance Insights](/azure/azure-sql/database/query-performance-insight-use) and common [performance recommendations](/azure/azure-sql/database/database-advisor-find-recommendations-portal) provided by Microsoft.

- [Implement retry logic](/azure/azure-sql/database/troubleshoot-common-connectivity-issues) using the SDK to mitigate transient errors impacting Azure SQL Database connectivity.

- Prioritize the use of service-managed keys when applying server-side Transparent Data Encryption (TDE) for at-rest encryption.
  - If customer-managed keys or client-side (AlwaysEncrypted) encryption is required, ensure keys are appropriately resilient with backups and automated rotation facilities.

- Consider the use of [point-in-time restore](/azure/azure-sql/database/recovery-using-backups#point-in-time-restore) as an operational playbook to recover from severe configuration errors.

**Azure Database For PostgreSQL**

- Use Flexible Server for business-critical workloads that require Availability Zone support.

- When using Elastic Clusters (Citus) for business critical workloads, enable High Availability mode to receive the 99.95% SLA guarantee.

- Use the [Elastic Clusters (Citus)](/azure/postgresql/flexible-server/concepts-elastic-clusters) server configuration to maximize availability across multiple nodes.

- Define a capacity model for the application to inform compute and storage resource requirements within the data platform.
  - Consider [Flexible Server Reserved Pricing](/azure/postgresql/flexible-server/concept-reserved-pricing) to provide potential cost optimizations.

## Caching for Hot Tier Data

An in-memory caching layer can be applied to enhance a data platform by significantly increasing read throughput and improving end-to-end client response times for hot tier data scenarios.

Azure provides several services with applicable capabilities for caching key data structures. This section focuses on scenarios where additional read performance and data access durability is required.

### Design Considerations

- A caching layer provides additional data access durability since even if an outage impacting the underlying data technologies, an application data snapshot can still be accessed through the caching layer.

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
  - Calculate a [health score](mission-critical-health-modeling.md) for regional cache components to observe health relative to business requirements and resource utilization.
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
