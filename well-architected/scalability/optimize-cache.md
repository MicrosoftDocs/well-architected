---
title: Cache data for performance optimization
description: Learn to examine caching considerations for performance optimization. Caching is a strategy where you store a copy of the data in front of the main data store.
author: martinekuan
ms.author: martinek
ms.date: 12/08/2021
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-efficiency
products:
  - azure-cache-redis
categories:
  - management-and-governance
---

# Cache data for performance optimization

Caching is a strategy where you store a copy of the data in front of the main data store. Advantages of caching include faster response times and the ability to serve data quickly, which can improve user experience. The cache store is typically located closer to the consuming client than the main store.

Caching is most effective when a client instance repeatedly reads the same data, especially if all the following conditions apply to the original data store:

- It remains relatively static.
- It's slow compared to the speed of the cache.
- It's subject to a high level of contention.
- It's far away when network latency can cause access to be slow.

Caching can dramatically improve performance, scalability, and availability. The more data that you have and the larger the number of users that need to access this data, the greater the benefits of caching become. You get this benefit because caching reduces the latency and contention that's associated with handling large volumes of concurrent requests in the original data store.

Incorporating appropriate caching can also help reduce latency by eliminating repetitive calls to microservices, APIs, and data repositories. The key to using a cache effectively lies in determining the most appropriate data to cache, and caching it at the appropriate time. Data can be added to the cache on demand the first time it's retrieved by an application. Using this method means that the application needs to fetch the data only once from the data store, and that subsequent access can be satisfied by using the cache. To learn more, see [Determine how to cache data effectively](/azure/architecture/best-practices/caching#determine-how-to-cache-data-effectively).

For details, see [Caching](/azure/architecture/best-practices/caching).

## Azure Cache for Redis

[Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview) improves the performance and scalability of an application. It processes large volumes of application requests by keeping frequently accessed data in the server memory that can be written to and read from quickly. Based on the [Redis](https://redis.io/) software, Azure Cache for Redis brings critical low-latency and high-throughput data storage to modern applications.

Azure Cache for Redis also improves application performance by supporting common application architecture patterns. Some of the most common include data cache and content cache. For the most common patterns and their descriptions, see [Common application architecture patterns](/azure/azure-cache-for-redis/cache-overview#key-scenarios).

## Azure Content Delivery Network (CDN)

A content delivery network (CDN) is a distributed network of servers that can efficiently deliver web content to users. A CDN stores cached content on edge servers in point-of-presence (POP) locations that are close to end users, to minimize latency. To learn more about CDN, see [What is a content delivery network on Azure?](/azure/cdn/cdn-overview)

## Next
> [!div class="nextstepaction"]
> [Partition](./optimize-partition.md)
