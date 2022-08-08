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

[Intro paragraph]

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Storage efficiency

### Design considerations

- Storing a lot of uncompressed data can result in unnecessary bandwidth waste, and increases the requirements on storage capacity.

- Querying big databases or retrieving a lot of information at the same time can have a performance penalty. Ideally, apps are optimized for query performance.

- The carbon impact of data retrieved from hot storage can be higher than data from cold- or archive storage. Designing  solutions with the correct data access pattern in mind can enhance the application's carbon efficiency.

### Design recommendations

- Enable storage compression.
  - A solution to reduce the storage requirements, including both capacity and required bandwidth to write or retrieve data. For example, [compressing files in Azure Front Door](/azure/frontdoor/standard-premium/how-to-compression) and [compressing files in Azure CDN ](/azure/cdn/cdn-improve-performance)
  - Compression is a well-known design technique to improve network performance.
  - Consider the tradeoff of compression: Does the benefit of compression outweigh the increased _carbon_ cost in the resources (CPU, RAM) needed to perform the compression/decompression?
  
- Optimize database query performance.
  - Reduces the latency of data retrieval while also reducing the load on the database.
  - Understand the [query performance for Azure SQL Databases](/azure/azure-sql/database/query-performance-insight-use)
  - There are many well-known ways to optimize data query performance, for example [tuning apps and databases for performance in an Azure SQL database](/azure/azure-sql/database/performance-guidance).
  - Consider that it may require fine-tuning to achieve optimal results.

- Use [storage best suited for the application's data access patterns](/azure/architecture/guide/design-principles/use-best-data-store).
  - Make sure your most frequently data is stored in hot storage, making it easy to retrieve and does not require more processing to access.
  - Data that is infrequently used should be stored in cold storage or offline archive storage, using less energy.

- Store only what is relevant.
  - Implement policies to streamline the process of storing and keeping relevant information. [Microsoft Purview](/azure/purview/overview) can help label data and add time-based purging to automatically delete it after a retention period. Additionally, this helps you stay in control of your data and reduces the amount of data to process and transfer.

## Next step

Review the design considerations for security.

> [!div class="nextstepaction"]
> [Security](sustainability-security.md)