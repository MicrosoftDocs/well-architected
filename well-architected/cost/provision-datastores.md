---
title: Data store cost estimates
description: Get detailed information about making cost estimates for your Azure data stores. Learn cost strategies for database design.
author: martinekuan
ms.author: martinek
ms.date: 04/14/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-sql-database
  - azure
  - azure-cost-management
ms.custom:
  - article
  - internal-intro
---

# Data store cost estimates

Most cloud workloads adopt the *polyglot* persistence approach. Instead of using one data store service, they use a mix of technologies. You can achieve optimal cost benefit from using this approach.

Each Azure data store has a different billing model. To establish a total cost estimate:

- [Identify the business transactions and their requirements](#guidelines-identify-the-business-transactions-and-their-requirements)
- [Break each transaction into operations](#guidelines-break-each-transaction-into-operations)
- [Identify a data store appropriate for the type of data](#guidelines-identify-a-data-store-appropriate-for-the-type-of-data)

Perform these steps for each workload separately.

For example, take an e-commerce application. It needs to store data for transactions such as orders, payments, and billing. The data structure is predetermined and not expected to change frequently. Data integrity and consistency are crucial. There's a need to store product catalogs, social media posts, and product reviews. In some cases, the data is unstructured, and is likely to change over time. Media files must be stored and data must be stored for auditing purposes.

Learn about data stores in [Understand data store models](/azure/architecture/guide/technology-choices/data-store-overview).

## Identify the business transactions and their requirements

The following list of questions address the requirements that can have the greatest effect your cost estimate. For example, your monthly bill might be within your budget now, but if you scale up or add storage space later, your cost might increase well over your budget.

- Does your data need to be migrated to on-premises, external data centers, or other cloud-hosting environments?
- What type of data are you intending to store?
- How large are the entities you need to store?
- What is the overall amount of storage capacity you need?
- What kind of schemas apply to your data, for example, fixed schema, schema-on-write, or schema-on-read?
- What are your data performance requirements, for example, acceptable response times for querying and aggregation of data once ingested?
- What level of fault-tolerance do you need to provide for data consumers?
- What kind of data replication capabilities do you require?
- Do the limits of a particular data store support your requirements for scale, number of connections, and throughput?
- How many instances need to run to support your uptime and throughput requirements? Consider operations costs in this calculation.
- Can you partition your data to store it more cost effectively? For example, can you move large objects out of an expensive relational database into an object store?

There are other requirements that might not have as great of an effect on your cost. For example, the US East region is only slightly lower than Canada Central. See [Criteria for choosing a data store](/azure/architecture/guide/technology-choices/data-store-considerations) for other business requirements.

Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to determine different cost scenarios.

### What are the network requirements that might affect cost?

- Do you need to restrict or otherwise manage access to your data from other network resources?
- Does data need to be accessible only from inside the Azure environment?
- Does the data need to be accessible from specific IP addresses or subnets?
- Does the data need to be accessible from applications or services hosted on-premises or in other external data centers?

## Break each transaction into operations

For example, these distinct operations might process one business transaction:

- 10-15 database calls
- Three append operations on blob
- Two list operations on two separate file shares

### How does data type in each operation affect cost?

- Consider Azure Blob Storage Block Blobs instead of storing binary image data in Azure SQL Database. Blob storage is cheaper than Azure SQL Database.
- If your design requires SQL, store a lookup table in SQL Database and retrieve the document when needed to serve it to the user in your application middle tier. SQL Database is highly targeted for high-speed data lookups and set-based operations.
- The hot access tier of Azure Block Blob Storage cost is cheaper than the equivalent size of the Premium SSD volume that has the database.

For more information, see [Understand data store models](/azure/architecture/guide/technology-choices/data-store-overview).

## Identify a data store appropriate for the type of data

> [!NOTE]
> An inappropriate data store or one that is mis-configured can have a huge cost impact on your design.

### Relational database management systems cost

When you need strong consistency guarantees, we recommend relational database management systems (RDBMS). An RDBMS typically supports a schema-on-write model, where the data structure is defined ahead of time, and all read or write operations must use the schema.

#### How can I save money if my data is on-premises and already on SQL server?

If the on-premises data is already on a SQL server, it might be a natural choice. The on-premises license with Software Assurance can be used to bring down the cost if the workload is eligible for [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). This option applies for Azure SQL Database (PaaS) and SQL Server on Azure Virtual Machines (IaaS).

For open-source databases such as MySQL, MariaDB, or PostGreSQL, Azure provides managed services that are easy to provision.

#### What are some design considerations that affect cost?

- If the SLAs don't allow for downtime, can a read-only replica in a different region enable business continuity?
- If the database in the other region must be read/write, how is the data replicated?
- Does the data need to be synchronous, or could consistency allow for asynchronous replication?
- How important is it for updates made in one node to appear in other nodes, before further changes can be made?

Azure storage has several options to make sure data is copied and available when needed. [Locally redundant storage (LRS)](/azure/storage/common/storage-redundancy#locally-redundant-storage) synchronously replicates data in the primary region. If the entire primary center is unavailable, the replicated data is lost. At the expensive end, [Geo-zone-redundant storage](/azure/storage/common/storage-redundancy#geo-zone-redundant-storage) (GZRS) replicates data in availability zones within the primary region and asynchronously copied to another paired region. Databases that offer geo-redundant storage, such as SQL Database, are more expensive. Most other OSS RDBMS databases use LRS storage, which contributes to the lower price range.

For more information, see [Automated backups in Azure SQL Database](/azure/sql-database/sql-database-automated-backups?tabs=single-database).

[Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) offers five consistency levels: *strong*, *bounded staleness*, *session*, *consistent prefix*, and *eventual* consistency. Each level provides availability and performance tradeoffs. Comprehensive SLAs back each level. The consistency level itself doesn't affect cost.

#### How can I minimize compute cost?

Higher throughput and IOPS require higher compute, memory, I/O and storage limits. These limits are expressed in a vCore model. With higher vCore number, you buy more resources, so the cost is higher. Azure SQL Database has more vCores and allows you to scale in smaller increments. Azure Database for MySQL, PostgreSQL, and MariaDB have fewer vCores and scaling up to a higher vCore can cost more. MySQL provides in-memory tables in the *Memory Optimized* tier, which can also increase the cost.

All options offer a consumption and provisioned pricing models. With preprovisioned instances, you save more if you can commit to one or three years.

#### How is primary and backup storage cost calculated?

With Azure SQL Database, the initial 32 GB of storage is included in the price. For the other listed options, you need to buy storage separately and might increase the cost depending on your storage needs.

For most databases, there's no charge for the price of backup storage that is equal in size to primary storage. If you need more backup storage, you incur more cost.

### Key/value and document databases cost

We recommend Azure Cosmos DB for key/value stores and document databases. Azure Cache for Redis is also recommended for key/value stores.

For [Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/), here are some considerations that affect cost:

- Can storage and item size be adjusted?
- Can index policies be reduced or customized to bring down the extra cost for writes and eliminate the requirement for more throughput capacity?
- If data is no longer needed, can it be deleted from the Azure Cosmos DB account? As an alternative, you can migrate the old data to another data store such as Azure blob storage or Azure data warehouse.

For [Azure Cache for Redis](https://azure.microsoft.com/pricing/details/cache/), there's no upfront cost, no termination fees, you pay only for what you use, and billing is per-hour.

### Graphic databases cost

Cost considerations for graphic database stores include storage required by data and indexes used across all the regions. For example, graphs need to scale beyond the capacity of a single server, and make it easy to look up the index when processing a query.

The Azure service that supports this functionality is Azure Cosmos DB for Apache Gremlin. Cost is limited to the [Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) usage. You pay for the operations you perform on the database and for the storage consumed by your data. Charges are determined by the number of provisioned containers, the number of hours the containers were online, and the provisioned throughput for each container.

If the on-premises data is already on an SQL server on Azure Virtual Machines (IaaS), the license with Software Assurance can be used to bring down the cost if the workload is eligible for [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

### Data analytics cost

Cost considerations for data analytics stores include data storage, multiple servers to maximize scalability, and the ability to access large data as external tables.

Azure has many services that support data analytics stores:

- [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/)
- [Azure Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/)
- [Azure Data Explorer](https://azure.microsoft.com/pricing/details/data-explorer/)
- [Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/)
- [Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)
- [Azure Databricks](https://azure.microsoft.com/pricing/details/databricks/)

As an example of use, historical data is typically stored in data stores such as blob storage or Azure Data Lake Storage Gen2. Azure Synapse, Databricks, or HDInsight access these stores as external tables.

When using Azure Synapse, you only pay for the capabilities that you opt in to use. During public preview, there's no cost for provisioning an Azure Synapse workspace. Enabling a managed virtual network and Customer Managed Keys might incur a workspace fee after public preview. Pricing of workspaces with other capabilities will be announced at a future date.

### Column family database cost

The main cost consideration for a column family database is that it needs to be massively scalable.

The Azure services that support column family databases are Azure Cosmos DB for Apache Cassandra and HBase in HDInsight.

For Azure Cosmos DB for Apache Cassandra, you pay the cost of [Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/), which includes database operations and consumed storage. Azure Cosmos DB for Apache Cassandra is open-source.

For HBase in HDInsight, you pay the cost of [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), which includes instance size and number. HBase is open-source.

### Search engine database cost

Cost incurs for a search engine database when applications need to search for information held in external data stores. It also needs to index massive volumes of data and provide near real-time access to these indexes.

[Azure Cognitive Search](https://azure.microsoft.com/pricing/details/cognitive-services/) is a search service that uses AI capabilities to identify and explore relevant content at scale.

### Time series database cost

The main cost considerations for a time series database is the need to collect large amounts of data in real time from a large number of sources. Although the records written to a time series database are generally small, there are often a large number of records, and total data size can grow rapidly, which drives up cost.

[Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/) might be the best option to minimize cost.

### Object storage cost

Cost considerations include storing large binary objects such as:

- Images
- Text files
- Video and audio streams
- Large application data objects and documents
- Virtual machine disk images

Azure services that support object storage are [Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) and [Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

### Shared files cost

The main cost consideration is having the ability to access files across a network. For example, given appropriate security and concurrent access control mechanisms, sharing data in this way can enable distributed services to provide highly scalable data access for performing basic, low-level operations such as simple read and write requests.

The Azure service that supports shared files is [Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).
