---
title: Data and storage design considerations for sustainable workloads on Azure
description: This design area explores considerations around storage design for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - storage
products: Azure
ms.custom:
  - sustainability
---

# Data and storage design considerations for sustainable workloads on Azure

Data storage in Azure is a crucial component of most provisioned workloads. Learn how to design for a more sustainable data storage architecture and optimize existing deployments.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Storage efficiency

Build solutions with efficient storage to increase performance, lower the required bandwidth, and minimize unnecessary storage design climate impact.

### Design considerations

- Storing much uncompressed data can result in unnecessary bandwidth waste and increase the storage capacity requirements.

- Querying extensive databases or retrieving much information simultaneously can have a performance penalty. Ideally, apps should optimize for query performance.

- The carbon impact of data retrieved from hot storage can be higher than data from cold- or archive storage. Designing solutions with the correct data access pattern can enhance the application's carbon efficiency.

- Backup is a crucial part of reliability. However, storing backups indefinitely can quickly allocate much unnecessary disk space. Consider how you plan backup storage retention.

### Design recommendations

- Enable storage compression.
  - A solution to reduce the storage requirements, including both capacity and required bandwidth to write or retrieve data. For example, [compressing files in Azure Front Door](/azure/frontdoor/standard-premium/how-to-compression) and [compressing files in Azure CDN](/azure/cdn/cdn-improve-performance)
  - Compression is a well-known design technique to improve network performance.
  - Consider the tradeoff of compression: Does the benefit of compression outweigh the increased _carbon_ cost in the resources (CPU, RAM) needed to perform the compression/decompression?
  
- Optimize database query performance.
  - Reduces the latency of data retrieval while also reducing the load on the database.
  - Understand the [query performance for Azure SQL Databases](/azure/azure-sql/database/query-performance-insight-use)
  - There are many well-known ways to optimize data query performance, for example [tuning apps and databases for performance in an Azure SQL database](/azure/azure-sql/database/performance-guidance).
  - Consider that it may require fine-tuning to achieve optimal results.

- Use [storage best suited for the application's data access patterns](/azure/architecture/guide/design-principles/use-best-data-store).
  - Make sure your most frequent data is stored in hot storage, making it easy to retrieve and doesn't require more processing to access.
  - Infrequently used data should be stored in cold or offline archive storage, using less energy.

- Only store what is relevant.
  - Implement policies to streamline the process of storing and keeping relevant information. [Microsoft Purview](/azure/purview/overview) can help label data and add time-based purging to delete it after a retention period automatically. Additionally, this lets you stay in control of your data and reduces the amount of data to process and transfer.
  
- Reduce the number of recovery points in place for VM backups.
  - For example, if [soft delete](/azure/backup/backup-azure-security-feature-cloud) is enabled, then the expired recovery points aren't cleaned up. Read more about the [impact of expired recovery points for items in soft deleted state](/azure/backup/manage-recovery-points#impact-of-expired-recovery-points-for-items-in-soft-deleted-state).

## Next step

Review the design considerations for security.

> [!div class="nextstepaction"]
> [Security](sustainability-security.md)
