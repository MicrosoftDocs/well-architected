---
title: Recommendations for optimizing data costs
description: Learn how to optimize data costs for a workload by aligning data spending to data priority.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing data costs

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:10](checklist.md)| Optimize data costs. Data spending with data priority. Data optimization should include improvements to data management (tiering and retention), volume, replication, backups, file formats, and storage solutions.|
|---|---|

This guide describes the recommendations for optimizing data costs for a workload. Optimizing data costs involves minimizing the expenses related to the storage and management of data according to its significance and access frequency. Appropriate data management can significantly reduce overhead costs and align spending with data utility. Neglecting to optimize data costs can lead to inflated expenses, inefficient resource allocation, and financial waste due to misaligned storage solutions and unnecessary data retention.

**Definitions**

| Term | Definition |
|----|----|
| Data lifecycle management | The process of managing data throughout its entire lifecycle, from creation to deletion. This process involves organizing, storing, protecting, and archiving data based on its value and usage patterns.|
| Data redundancy | The practice of storing duplicate copies of data across multiple storage systems or locations. The purpose of data redundancy is to improve data availability and fault tolerance.|
| Data tiering | A storage strategy that involves categorizing data based on its access frequency and storing it on storage tiers accordingly. |
| Retention policy | The duration for which data should be retained before it can be deleted. It specifies the time period during which data must be preserved to meet legal, regulatory, or business requirements. |

## Key design strategies

Within a specific workload, you optimize data costs by reducing the expenses associated with storing and managing data. There are various strategies and best practices to minimize data storage and processing costs. The goal is to align data costs with data priority. You need to assign cost tiers to types of data based on their importance or frequency of access.

The primary drivers for the cost of workload data are access frequency, access latency, and storage amount. The following guidance contains strategies for optimizing costs across these cost drivers.

### Take an inventory of data

Before you can optimize the cost of your data, you need to generate an inventory of data. Examine data access and determine its importance within your workload and its operations. Identify which data is accessed frequently and which data is accessed less frequently. The following inventory actions can help you allocate storage resources effectively:

- *Collect data access information:* Conduct a data audit to identify and catalog all data stores. Determine the value of data sets based on their importance to business operations, return on investment, and frequency of use. Gather access logs, usage metrics, or analytics from your data storage solutions.

- *Identify data types:* Categorize data based on its type, such as personal data, financial data, intellectual property, or operational data. Understand the sensitivity and criticality of each data type.

- *Identify access patterns:* Identify the patterns in data access, such as daily, weekly, or monthly usage patterns. You should understand latency, file sizes, and data freshness requirements for that data.

### Prioritize data

Data prioritization is the process of categorizing and assigning importance levels to types of data based on sensitivity and criticality. Data priority should align with the importance of the environment. For example, production data is more important than preproduction data.

Assess the importance of various types of data to your workload by using these steps:

1. *Define priority levels:* Establish priority levels for data (such as high, medium, and low) based on its value to the organization, regulatory requirements, and potential effect of data loss. The goal is to align data priority to the appropriate data solution.

1. *Assign labels:* Label each data set with its sensitivity and criticality. You can apply labels at the row, column, or file level, depending on the data structure and usage. For databases, you can use a special tool to label and relate the sensitivity and criticality of data to specific rows and columns. This approach provides granular control over the management and access of data.

### Optimize data management

Data management is the process of storing, moving, and securing workload data. By optimizing data management, you can align spending to data priority and derive more value from your data. Consider the following strategies for data management.

#### Optimize data lifecycle management

It's important to manage data throughout its lifecycle. Stages of the lifecycle include data creation (or acquisition), storage, usage, sharing, retention, and disposal (deletion or archiving). The goal of data lifecycle management is to optimize data storage solutions while complying with relevant regulations and policies.

Data storage has three critical cost components:

- *Storage cost*: The expense associated with storing data, such as per gigabyte.

- *Transaction cost*: Costs linked to data operations, such as write operations, read operations, and data retrieval (per gigabyte). Reading and writing data might have different costs.

- *Latency cost*: The expense associated with the speed or delay in accessing the data.

The following considerations are foundational to data lifecycle management:

- *Use data tiering:* The goal of data tiering is to align access and retention with the most cost-effective storage tier. Storage tiers range from frequent/immediate access (hot) to infrequent/delayed access (cold).

  It costs more to use a tier that doesn't align with data access and retention needs. For example, data that your application accesses frequently should be in hot storage. Data that your application accesses infrequently should be in cold storage. Effectively managing these aspects helps ensure efficient data storage.

- *Consider compliance requirements:* Implementing data tiering requires careful consideration of compliance requirements and data governance policies. Compliance and legal requirements often drive data access and retention. Establish data retention policies to ensure compliance with legal, regulatory, and business requirements.

- *Define data lifecycle policies*. Data lifecycle policies specify when and how data should be moved between storage tiers based on predefined criteria. These policies ensure that you keep data in the appropriate tier for the required duration. For example, a policy can state that data must be retained in the hot tier for 30 days, in the cool tier for 90 days, and in the archive tier for one year. Set the retention period based on factors such as legal requirements, industry regulations, or internal policies.

- *Use automation*: Retention policies can trigger the movement of data between tiers. You should automate policies by using platform features before you build any custom solution.

  When the retention period for a particular tier expires, the policy can automatically move the data to the next lower-cost tier. For example, when the retention period for the hot tier ends, the policy can move the data to the cool tier. The policy ensures that data is continuously optimized based on its access patterns and cost requirements.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Managing data retention policies requires ongoing monitoring and maintenance. It can introduce more overhead for data management processes. It might also affect storage costs. Longer retention periods or the use of higher-cost storage tiers can increase storage expenses.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: A poor implementation of data lifecycle management could lead to data loss or limited access to critical data. You should have proper backup and recovery mechanisms in place to mitigate the risk of data loss.

#### Optimize data segmentation

Optimizing data segmentation involves strategically organizing data into distinct segments and consolidate similar data types to efficiently allocate storage resources. It allows you to tailor allocation of storage resources to data priority.

To effectively optimize data segmentation, you categorize data by type and usage pattern. Then you place the data segments on the most-effective solution depending on their operational similarities and requirements. For example, you place data that requires high-performance storage on resources with faster retrieval time. Archival data uses a lower-cost resource with slower retrieval time.

This approach ensures that high-demand data uses faster storage for optimal performance and less accessed data uses cheaper storage. Similarly, when data types share usage patterns, you should group them together on a single resource to reduce overhead, simplify management, and improve data handling.

#### Minimize data transfer

Minimizing data transfer refers to the reduction of data movement across networks to decrease data transfer costs. It reduces the volume of data that the workload moves and lowers network usage fees. To minimize data transfer, consider the following recommendations:

- *Use the right location.* place data geographically closer to its users. Data proximity reduces network travel, which speeds up access and optimizes costs.
- *Use caching.* Consider the benefits of caching to minimize data transfer.
- *Use a content delivery network.* A content delivery network can store frequently read static data closer to users. It reduces data movement across the network and helps offload bandwidth usage.

#### Optimize security and compliance

Certain production data demands higher security and compliance requirements. These measures might impose extra costs related to data protection, encryption, backup, retention, and auditing.

You must ensure that changes in data storage solutions adhere to these requirements. Data that has lower security and compliance requirements often presents an opportunity to optimize cost.

### Optimize data volume

Finding strategies to decrease the amount of data that you store can help reduce costs. By changing the accessibility of the data and implementing the following techniques, you can effectively optimize the volume of your stored data:

- *Capture less data*: Take a closer look at the data you're capturing. Determine if any of it's unnecessary for your purposes. Modify your process, settings, or configurations to capture only the essential data.

- *Compress data*: Compression saves money by reducing the size of data. It's most effective in write-once, read-never or read-rarely scenarios. It's more suitable for colder storage.

  :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Both compression and decompression of data increase CPU time.

- *Delete unneeded data*: Implement policies to streamline the process of storing relevant information. Evaluate the retention period for backups and snapshots, and delete data that you no longer need. You might want to have a process that leads up to eventual data deletion, such as first archiving data and enabling a soft-delete period. Always consider recoverability before deleting data.

- *Deduplicate data*: Implement data deduplication techniques to eliminate redundant data. Deduplication reduces storage requirements by ensuring that you store only unique data blocks, so you save costs. Use hashing algorithms and comparison of data chunks. Regularly run deduplication processes to identify and eliminate duplicate data.

- *Optimize user behavior*: In workloads that collect user-generated data, educate users on the importance of efficient data storage. Encourage them to regularly review and delete unnecessary files and data. Implement storage quotas or pricing models that discourage excessive data storage.

### Optimize data replication

Data replication involves creating multiple copies of data and storing them in other geographic locations or zones for reliability. Replication ensures that if one location or zone experiences a failure or outage, you can still access the data from the replicated copies in other locations.

This redundancy helps improve the availability and resilience of data. It minimizes the risk of data loss and downtime.

To optimize data replication for cost optimization, consider the following guidelines:

- *Evaluate data replication requirements*: Assess the specific needs of your workload and determine the level of data replication that it requires. Consider factors such as data criticality, recovery time objectives (RTOs), and recovery point objectives (RPOs).

- *Choose the right replication strategy*: Select a replication technology that aligns with your goals for cost optimization. Consider the service-level agreement (SLA) requirements for your workload.

  Evaluate options such as synchronous replication, asynchronous replication, or a combination of both. Base the decision on factors like data consistency requirements and network bandwidth considerations. Assess the level of availability that you need for your workload, and evaluate the need for zonal versus regional redundancy.

- *Optimize network bandwidth*: Minimize the usage of network bandwidth by implementing compression and data deduplication techniques. These techniques can reduce the amount of data transferred during replication, which can save costs.

- *Monitor and optimize replication frequency*: Regularly review and adjust the replication frequency based on the changing needs of your workload. Fine-tuning the replication frequency can help optimize costs by reducing unnecessary replication overhead.

### Optimize backups

A backup is a periodic snapshot or copy of data that you can create and store separately from the primary storage. If there's data corruption, accidental deletion, or system failure, you can use backups to restore the data to its previous state.

Here are some techniques for optimizing backups:

- *Data classification*: Classify your data based on its importance and prioritization for backup. Classification helps you to focus resources on backing up critical data while minimizing backup costs for data that's less important.

- *Incremental backups*: Instead of performing full backups every time, consider implementing incremental backups. Incremental backups capture only changes made since the last backup, which can reduce storage and network bandwidth requirements.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Incremental backups require more steps and time to restore data. You need to restore the full backup first and then apply each incremental backup in sequence until you reach the desired restore point.

- *Backup compression*: Enable compression during the backup process to reduce the size of backup files. Compressed backups require less storage space, so you can save costs.

- *Backup storage tiers*: Evaluate your backup retention policies and consider moving older backups to lower-cost storage tiers, such as cold storage or archive storage. Storing less frequently accessed backups in cost-effective storage options helps optimize costs.

- *Backup retention period*: Review and adjust the retention periods for your backups based on business requirements and compliance regulations. Maintaining backups for longer durations might lead to extra storage costs.

- *Backup frequency*: Analyze the backup frequency for various types of data. Adjust the backup schedule based on the frequency of data changes and the importance of the data. These practices help eliminate unnecessary backups and reduce storage costs.

### Optimize file formats

File formats influence cost optimization by optimizing input/output (I/O) patterns and query patterns of your data. Some file formats cater to particular scenarios. Aligning the file format with your workload requirements can improve the workload's performance.

Here are considerations for common formats:

- *Avro*: The Avro file format is a good choice when you're dealing with write-heavy I/O patterns or when query patterns necessitate fetching multiple rows of records in their entirety. Avro's serialization and deserialization processes are efficient, so it's compatible with message buses like Kafka that produce a series of events and messages in quick succession.

- *Parquet and Optimized Row Columnar (ORC)*: The Parquet and ORC file formats excel in scenarios of read-heavy I/O patterns or when the query patterns focus on specific columns of the records.

  Both formats are columnar storage, which means that data is stored column by column rather than row by row. Columnar storage allows for improved compression and efficient read operations. Only the required columns need to be fetched, so you avoid unnecessary I/O for irrelevant data.

### Optimize storage solutions

Evaluate and select the most appropriate storage methods and systems for your data. This effort might include switching databases, using different storage types, or adding caching mechanisms. Ease of management is another factor to consider when you're choosing a storage solution.

By tailoring storage solutions to the specific needs and characteristics of the data, you can achieve better cost-effectiveness while meeting performance and scalability demands. There are costs associated with switching databases or swapping services, but storing data in the wrong storage solution can cost you extra money.

Here are a few use cases:

- *Switching databases*: You could consider switching to a database system that better suits your needs. For instance, if you're using a relational database, you might explore the option of moving to a NoSQL database if your data is more document oriented or requires flexible schemas.

- *Moving from a relational database to a flat file store*: In some cases, storing data in flat files instead of a traditional relational database can provide advantages such as simplicity and cost-effectiveness. Flat files are well suited for certain types of data, such as log files or data that doesn't require complex querying. For example, you can store binary images in a SQL database, but it's more cost-effective to store them in a storage service that's specifically for handling binary data.

- *Moving from infrastructure as a service (IaaS) to platform as a service (PaaS)*: IaaS database solutions can be time-consuming and resource-intensive properties that divert a technical team's attention from core tasks. The growth in data volume and the challenges of manual scaling, backups, and infrastructure maintenance can make a PaaS solution more cost-effective and efficient.

- *Adding a cache*: To reduce resource usage on the main database server, consider using a cache solution for caching complex query results. Rightsizing the database server might help in optimizing the cost. With applicable use cases, consider using time to live (TTL) with the cached data to reduce the storage needs and reduce the cost.

- *Query-optimized versus data storage stores*: Query-optimized stores are designed for fast data retrieval and analysis. They focus on quick data ingestion and reads but not frequent updates. They're great for time-series data and rapid access to recent data, but not for heavy transactional tasks.

  Data storage stores handle large volumes of flexible data, especially unstructured or semistructured data. Although data storage stores can support analytics, complex tasks might need specialized databases. They're best for storing lots of variable data like logs or user-generated content in scenarios like NoSQL use cases.

## Azure facilitation

**Taking an inventory of data**: [Microsoft Purview](/purview/purview) is a family of data governance, risk, and compliance solutions that can help your organization govern, protect, and manage your entire data estate. Microsoft Purview solutions provide integrated coverage and help address the recent increases in remote user connectivity, the fragmentation of data across organizations, and the blurring of traditional IT management roles.

**Optimizing data management**: Azure Storage and Azure Data Lake Storage have different [data access tiers](/azure/storage/blobs/access-tiers-overview). They also offer [data lifecycle management policies](/azure/storage/blobs/lifecycle-management-overview) that automate data tiering and retention.

You can use a rule-based policy to transition blob data to the appropriate access tiers or to expire data at the end of its lifecycle. This policy allows you to transition blobs from cool (or cold) to hot immediately when they're accessed, to optimize for performance.

**Optimizing backups**: The [Azure Backup](/azure/backup/backup-overview) service provides multiple capabilities to streamline your backups. It offers features such as native database backup and storage backup through disk snapshots. It supports virtual machine backup, long-term retention, and backup management.

Here are some of the service's features:

- *Monitoring*: You can use Backup center as a single pane of glass to monitor your jobs and backup inventory on a day-to-day basis. Backup center provides an interface to Backup reports, which use Azure Monitor Logs and Azure workbooks.

- *Reports*: Backup reports offer the following capabilities:
  - Allocate and forecast consumed cloud storage.
  - Audit backups and restores.
  - Identify key trends at various levels of granularity.
  - Gain visibility and insights into cost optimization opportunities for your backups.

- *Reserved capacity*: [Azure Backup Storage](/azure/backup/backup-azure-reserved-pricing-optimize-cost) reserved capacity offers you a discount on capacity for backup data stored for the vault-standard tier when you commit to a reservation for either one year or three years. A reservation provides a fixed amount of backup storage capacity for the term of the reservation.

- *Archive tier*: You can use Azure Backup to store backup data, including long-term retention (LTR) backup data, according to the retention needs that your organization's compliance rules define. In most cases, the older backup data is rarely accessed and is stored only for compliance needs. Azure Backup supports the backup of LTR points in the [archive tier](/azure/backup/archive-tier-support), in addition to snapshots and the standard tier.

**Optimizing storage solutions**: Azure has many storage solutions. They offer various features and capabilities to help optimize costs based on your specific requirements. Azure has guidance to help you [choose the right data store](/azure/architecture/guide/technology-choices/data-store-decision-tree).

To choose the most suitable storage solution and configuration, it's important to assess your data access patterns, retention needs, and performance requirements. Regularly monitoring and optimizing your storage usage by using tools like Azure Advisor can help you further optimize costs.

## Organizational alignment

The Cloud Adoption Framework provides guidance for optimizing data costs for organizational analytics platforms.

For more information, see [Data lifecycle management](/azure/cloud-adoption-framework/scenarios/cloud-scale-analytics/govern-lifecycle).

## Related links

- [Recommendations for consolidation](consolidation.md)
- [Microsoft Purview](/purview/purview)
- [Data access tiers](/azure/storage/blobs/access-tiers-overview)
- [Data lifecycle management policies](/azure/storage/blobs/lifecycle-management-overview)
- [Azure Backup Storage](/azure/backup/backup-azure-reserved-pricing-optimize-cost)
- [Archive tier](/azure/backup/archive-tier-support)
- [Choose the right data store](/azure/architecture/guide/technology-choices/data-store-decision-tree)
- [Data lifecycle management](/azure/cloud-adoption-framework/scenarios/cloud-scale-analytics/govern-lifecycle)

## Cost Optimization checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
