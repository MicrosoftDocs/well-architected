---
title: Recommendations for optimizing data costs
description: Learn how to optimize data costs.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing data costs: CO 10

This guide describes the recommendations for optimizing data costs. It outlines the strategies to align data spending to data priority. It involves considering various factors such as data tiering, retention policies, and data redundancy to optimize the storage and management of data. Without optimization data costs, you may end up incurring higher storage, transfer, and transaction costs.

**Definitions**

| Term | Definition   |
|----|----|
| Data Lifecycle Management | The process of managing data throughout its entire lifecycle, from creation to deletion and involves organizing, storing, protecting, and archiving data based on its value and usage patterns.|
| Data Tiering | A storage strategy that involves categorizing data based on its access frequency and storing it on different storage tiers accordingly. |
| Retention Policy | Defines the duration for which data should be retained before it can be deleted. It specifies the time period during which data must be preserved to meet legal, regulatory, or business requirements. |
| Data Redundancy| The practice of storing duplicate copies of data across multiple storage systems or locations. The purpose of data redundancy is to improve data availability and fault tolerance.|

## Key strategies

Optimizing data cost in a workload refers to reducing the expenses associated with storing and managing data within a specific workload. There are various strategies and best practices to minimize data storage and processing costs. The goal is to align data costs with data priority. You need to assign different cost tiers to different types of data based on their importance or frequency of access. The primary drivers for workload data cost are access frequency, access latency, and storage amount. The following guidance contains strategies for optimizing costs across these main costs drivers.

### Take an inventory of data

Before you can optimize the cost of your data, you need to generate an inventory of data. Examine data access and determine its importance within your workload and its operations. Data examination involves identifying which data is frequently accessed and which data is accessed less frequently. By doing so, you can allocate storage resources effectively and optimize costs.

- *Collect data access information:* Conduct a data audit to identify and catalog all data stores and stores. Determine the value of data sets based on their importance to business operations, ROI, and frequency of use. Gather access logs, usage metrics, or analytics from your data storage solutions.
- *Identify data types:* Categorize data based on its type, such as personal data, financial data, intellectual property, or operational data. Understand the sensitivity and criticality of each data type.
- *Identify access patterns:* Identify the patterns in data access, such as daily, weekly, or monthly usage patterns. You should understand latency, file sizes, and data freshness requirements associated with that data.

### Prioritize data

Data prioritization is the process of categorizing and assigning importance levels to different types of data based on their sensitivity and criticality. Data priority should align with the importance of the environment. For example, production data is more important the preproduction data. Assess the importance of different types of data to your workload using these steps:

- *Define priority levels:* Establish priority levels for data (such as high, medium, and low) based on its value to the organization, regulatory requirements, and potential effect of data loss. The goal is to align data priority to appropriate data solution.
- *Assign labels:* Label each data set with its sensitivity and criticality. Labels can be applied at the row, column, or file level, depending on the data structure and usage. For databases, you can use a special tool to label and relate the sensitivity and criticality of data to specific rows and columns. It provides granular control over the management and access of data.

### Optimize data management

Data management refers to the process of storing, moving, and securing workload data. By optimizing data management, you can align spending to data priority and dervie more value from your data. Consider the following data management strategies:

#### Optimize data lifecycle management

Data lifecycle management refers to the process of managing data throughout its entire lifecycle, from creation or acquisition to deletion or archival. It involves various stages, including data creation, storage, usage, sharing, retention, and disposal. The goal of data lifecycle management is to optimize data storage solutions while complying with relevant regulations and policies. Data storage has three critical cost components:

- *Storage cost*: The expense associated with storing data, such as per GB.
- *Transaction cost*: Costs linked to data operations, such as write operations, read operations, and data retrieval (per GB). It's important to note that reading and writing data may have different costs.
- *Latency cost*: Represents the speed or delay in accessing the data.

Data tiering and data retention policies are foundational to data lifecycle management. Here are considerations for a data lifecycle management schema:

- *Use data tiering:* The goal of data tiering is to align access and retention with the most cost-effective storage tier. Storage tiers range from frequent-immediate access (hot) to infrequent-delayed access (cold). It costs more to use a tier that doesn’t align with data access and retention needs. For example, data that your application accesses frequently should be in hot storage. Data that your application accesses infrequently should be in cold storage. Effectively managing these aspects ensures efficient and cost-effective data storage.
- *Consider compliance requirements:* Implementing data tiering requires careful consideration of compliance requirements and data governance policies. Compliance and legal requirements often drive data access and retention requirements. Establish data retention policies to ensure compliance with legal, regulatory, and business requirements.
- *Define data lifecycle policies*. Data lifecycle policies that specify when and how data should be moved between storage tiers based on predefined criteria. For example, a policy can state that data must be retained in the hot tier for 30 days, in the cool tier for 90 days, and in the archive tier for one year. These policies ensure you keep data in the appropriate tier for the required duration. You should set the retention period based on factors such as legal requirements, industry regulations, or internal policies.
- *Use automation*: Retention policies can trigger the movement of data between tiers. You should automate policies, using platform features before custom building of any solution. When the retention period for a particular tier expires, the policy can automatically move the data to the next lower-cost tier. For example, when the retention period for the hot tier ends, the policy can move the data to the cool tier, and so on. The policy ensures that data is continuously optimized based on its access patterns and cost requirements.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Managing data retention policies require ongoing monitoring and maintenance. It can introduce more overhead for data management processes. They may also affect storage costs. Longer retention periods or the use of higher-cost storage tiers can increase storage expenses.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: A poor implementation of data lifecycle management could lead to data loss or limited access to critical data. You should have proper backup and recovery mechanisms in place to mitigate the risk of data loss.

#### Optimize data segmentation

Segment different types of data onto separate resources based on their characteristics and usage patterns. By doing so, you can allocate resources tailored to the needs of each data type. The segmentation can help optimize performance and cost. For example, you can place data that requires high-performance storage on resources with faster storage solutions, while less frequently accessed or archival data can be stored on lower-cost resources with slower storage solutions. This approach allows you to match the requirements and cost of each data type efficiently.

Alternatively, you can group similar types of data together and run them on the same resource. This strategy is beneficial when multiple data types have similar usage patterns or require similar storage solutions. By consolidating the data onto a single resource, you can reduce costs by eliminating the need for separate resources for each data type and potentially optimize resource utilization. Grouping similar data together can also simplify data management and improve efficiency in data processing. For more information, see [consolidation](consolidation.md).

#### Minimize data transfer

Ensure data is close to the client. Data proximity reduces network travel, speeding up access and optimizing costs. Choose the appropriate storage location. Select a location that is closest to where most network packets are going. Consider the benefits of caching to minimize data transfer. A content delivery network (CDN) can store frequently read static data closer to users. It reduces data movement across the network and helps offload bandwidth usage.

#### Optimize security and compliance

Certain production data demands higher security and compliance requirements. These measures may impose extra costs related to data protection, encryption, backup, retention, and auditing. You must ensure that changes in data storage solutions adhere to these requirements. Data that has lower security and compliance requirements often presents an opportunity to optimize cost.

### Optimize data volume

Optimizing data storage means finding strategies to decrease the amount of data you store, which can help reduce costs. By changing the accessibility of the data and implementing techniques such as data compression, data deduplication, and data archiving, you can effectively optimize your data storage and minimize storage costs.

- *Capture less data*: Take a closer look at the data that is currently being captured. Determine if all the data is necessary for your needs or if there's any unnecessary data being captured. Stop capturing unnecessary data: Once you have identified the data that isn't needed, modify your data capture process to exclude that data. Consider updating your data capture settings or configurations to only capture the essential data.
- *Compress data*: Compression saves money by reducing the size of data. It's most effective in write-once, read-never or read-rarely scenarios and is more suitable for colder storage.

  :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Compression requires more CPU time both during compression and when decompressing the data.

- *Delete unneeded data*: Implement policies to streamline the process of storing and keeping relevant information. Evaluate the retention period for backups and snapshots and delete data that is no longer needed. You often want to have a process that leads up to eventual data deletion, such as first archiving data and enabling a soft delete period. Always consider recoverability before deleting data.

- *Deduplicate data*: Implement data deduplication techniques to eliminate redundant data. Deduplication reduces storage requirements by storing only unique data blocks, resulting in cost savings. Use hashing algorithms and comparison of data chunks. Regularly run deduplication processes to identify and eliminate duplicate copies of data.
- *Optimize user behavior*: In workloads that collect user generated data, encourage users to be mindful of their storage usage and minimize the amount of data they store. Educate users on the importance of efficient data storage. Encourage them to regularly review and delete unnecessary files and data. Implement storage quotas or pricing models that discourage excessive data storage.

### Optimize data replication

Data replication involves creating multiple copies of data and storing them in different geographic locations or zones for reliability. Replication ensures that if one location or zone experiences a failure or outage, the data can still be accessed from the replicated copies in other locations. This redundancy helps improve the availability and resilience of data, minimizing the risk of data loss and downtime. To optimize data replication for cost optimization, consider the following guidelines:

- *Evaluate data replication requirements*: Assess the specific needs of your workload and determine the level of data replication required. Consider factors such as data criticality, recovery time objectives (RTOs), and recovery point objectives (RPOs) to determine the appropriate replication strategy.
- *Choose the right replication strategy*: Select a replication technology that aligns with your cost optimization goals. Consider the service level agreement (SLA) requirements for your workload. Evaluate options such as synchronous replication, asynchronous replication, or a combination of both. Base the decision on factors like data consistency requirements and network bandwidth considerations. Assess the level of high availability needed for your workload and evaluate the need for zonal versus regional redundancy.
- *Optimize network bandwidth*: Minimize network bandwidth usage by implementing compression and data deduplication techniques. These techniques can reduce the amount of data transferred during replication, resulting in cost savings.
- *Monitor and optimize replication frequency*: Regularly review and adjust the replication frequency based on the changing needs of your workload. Fine-tuning the replication frequency can help optimize costs by reducing unnecessary replication overhead.

### Optimize backups

Backups involve creating periodic snapshots or copies of data and storing them separately from the primary storage. If there's data corruption, accidental deletion, or system failure, backups can be used to restore the data to its previous state. Here are some steps to optimize backups:

- *Data classification*: Classify your data based on its importance and prioritization for backup. It allows you to focus resources on backing up critical data while minimizing backup costs for less important data.
- *Incremental backups*: Instead of performing full backups every time, consider implementing incremental backups. Incremental backups only capture changes made since the last backup, reducing storage and network bandwidth requirements.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: When it comes to restoring data, incremental backups require more steps and time to restore. You need to restore the full backup first and then apply each incremental backup in sequence until you reach the desired restore point.

- *Backup compression*: Enable compression during the backup process to reduce the size of backup files. Compressed backups require less storage space, resulting in cost savings.
- *Backup storage tiers*: Evaluate your backup retention policies and consider moving older backups to lower-cost storage tiers, such as cold storage or archive storage. It helps optimize costs by storing less frequently accessed backups in cost-effective storage options.
- *Backup retention period*: Review and adjust the retention periods for your backups based on business requirements and compliance regulations. Maintaining backups for longer durations may lead to extra storage costs, so optimize the retention periods to align with your regulatory and compliance needs.
- *Backup frequency*: Analyze the backup frequency for different types of data. Adjust the backup schedule based on the frequency of data changes and the importance of the data. It helps eliminate unnecessary backups and reduces storage costs.

### Optimize file formats

File formats influence cost optimization by optimizing input/output (I/O) patterns and query patterns. Some file Different file formats cater to various scenarios.

- *Consider using Avro*: Avro file format is a good choice when dealing with more write-heavy I/O patterns or when query patterns necessitate fetching multiple rows of records in their entirety. Its serialization and deserialization processes are efficient, making it compatible with message buses like Kafka that produce a series of events and messages in quick succession.
- *Consider using parquet and ORC*: In contrast, the Parquet and ORC file formats excel in scenarios characterized by read-heavy I/O patterns or when the query patterns zero in on specific columns of the records. Both formats are columnar storage, which means that data is stored column-by-column rather than row-by-row. It allows for improved compression and more efficient read operations, as only the required columns need to be fetched, avoiding unnecessary I/O for irrelevant data.

By aligning the file format with your workload requirements, you can realize cost optimization and see improved performance by catering to the specific I/O and query patterns of your data.

### Optimize storage solutions

You need to make sure you’re using the right storage solution for your data. Sometimes changing storage solutions is the best way to cost optimize. There are costs associated with switching databases or swapping services, but storing data in the wrong storage solution can cost you extra money. Here are a few use cases:

- *Switching database*: You could consider switching to a different database system that better suits your needs. For instance, if you have been using a relational database, you may explore the option of moving to a NoSQL database if your data is more document-oriented or requires flexible schemas.
- *From relational database to flat file store*: In some cases, storing data in flat files instead of a traditional relational database can provide advantages such as simplicity and cost-effectiveness. Flat files are well-suited for certain types of data, such as log files or data that doesn't require complex querying. For example, you can store binary images in a SQL database, but it's more cost effective to store them in a storage service specifically for handling binary data.
- *From infrastructure as a service to platform as a service*: IaaS database solutions can be time-consuming and resource-intensive, diverting technical team's attention from core tasks. The growth in data volume and the challenges of manual scaling, backups, and infrastructure maintenance can make a PaaS solution more cost-effective and efficient.
- *Adding a cache*: Consider using cache solution for caching complex query results and reduce resource usage on main database server. Right sizing the database server may help optimizing the cost. With applicable use cases, consider using time to live (TTL) with the cached data to reduce the storage needs and reduce the cost.
- *Query optimized versus data storage stores*: Query-optimized stores are designed for fast data retrieval and analysis, focusing on quick data ingestion and reads but not frequent updates. They're great for time-series data and rapid access to recent data but not for heavy transactional tasks. Data storage stores handle large volumes of flexible data, especially unstructured or semi-structured data. While they can support analytics, complex tasks may need specialized databases. They're best for storing lots of variable data like logs or user-generated content in scenarios like NoSQL use cases.

It's important to evaluate the specific requirements of your data and consider factors like performance, scalability, cost, and ease of management when choosing a storage solution.

## Azure facilitation

**Taking a data inventory**: [Microsoft Purview](https://learn.microsoft.com/purview/purview) is a family of data governance, risk, and compliance solutions that can help your organization govern, protect, and manage your entire data estate. Microsoft Purview solutions provide integrated coverage and help address the recent increases in remote user connectivity, the fragmentation of data across organizations, and the blurring of traditional IT management roles.

**Optimizing data lifecycle management**: Azure Storage and Data Lake Storage has [different data access tiers](/azure/storage/blobs/access-tiers-overview) and has [data lifecycle management policies](/azure/storage/blobs/lifecycle-management-overview) that automate data tiering and retention. It offers a rule-based policy that you can use to transition blob data to the appropriate access tiers or to expire data at the end of its lifecycle. It allows you to transition blobs from cool, or cold to hot immediately when they’re accessed, to optimize for performance. For more information, see [Data lifecycle management.](/azure/cloud-adoption-framework/scenarios/cloud-scale-analytics/govern-lifecycle)

**Optimizing backups**: Azure Backup is the native backup solution in Azure that provides multiple capabilities to streamline your backups. It offers features such as native database backup compatibility and storage backup using disk snapshots. It supports virtual machine backup, long-term retention, and backup management

- *Monitoring*: You can use Backup center as a single pane of glass to monitor your jobs and backup inventory on a day-to-day basis. Backup Center provides an interface to Backup Reports, which uses Azure Monitor Logs and Azure Workbooks.
- *Reports*: Backup Reports offers the following capabilities. You can allocate and forecast cloud storage consumed. You can audit backups and restores. You can identify key trends at different levels of granularity, and gain visibility and insights into cost optimization opportunities for your backups.
- *Reserved capacity*: [Azure Backup Storage](/azure/backup/backup-azure-reserved-pricing-optimize-cost) reserved capacity offers you a discount on capacity for backup data stored for the vault-standard tier when you commit to a reservation for either one year or three years. A reservation provides a fixed amount of backup storage capacity for the term of the reservation.
- [*Archive tier*](/azure/backup/archive-tier-support): Customers rely on Azure Backup to store backup data including their Long-Term Retention (LTR) backup data as per the retention needs defined by the organization's compliance rules. In most cases, the older backup data is rarely accessed and is only stored for compliance needs. Azure Backup supports backup of Long-Term Retention points in the archive tier, in addition to Snapshots and the Standard tier.

**Optimizing storage solutions**: Azure has many different storage solutions. They offer various features and capabilities to help optimize costs based on your specific requirements. Azure has guidance to help you [choose the right data store](/azure/architecture/guide/technology-choices/data-store-decision-tree). It's important to assess your data access patterns, retention needs, and performance requirements to choose the most suitable storage solution and configuration. Additionally, regularly monitoring and optimizing your storage usage with tools like Azure Advisor can help you further optimize costs
