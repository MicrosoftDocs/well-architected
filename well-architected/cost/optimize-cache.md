---
title: Use cached data
description: Learn what caching is and its importance and advantages along with the challenges when used incorrectly.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/06/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-optimization
ms.custom:
  - article
  - internal-intro
products:
  - azure
  - azure-cost-management
---

# Caching data

Caching is a process that stores a copy of the data in front of the main data store. The cache store locations are closer to the consumer than the main store. Advantages of caching include faster response times and the ability to serve data quickly, which saves on the overall cost. Check the built-in caching features of Azure services used in your architecture. Azure also offers caching services such as Azure Cache for Redis or Azure CDN.

For information about what data is suitable for caching, see [Caching guidance](/azure/architecture/best-practices/caching).

## Lower costs associated with reliability and latency

Caching is a cost-effective way to store data, provide reliability, and reduce network latency.

- The type of data helps you determine if you need the complex capabilities of the backend data store, such as data consistency. For fully static data, store it in a caching store. If the data doesn't change frequently, consider placing a copy of the data in a caching store and refresh it from time to time.

    > For example, an application stores images in blob storage. Then every time the application requests an image, the business logic generates a thumbnail from the main image and returns it to the caller. If the main image doesn't change too often, then return the previously generated thumbnails stored in a cache. This way, you can save on resources required to process the image and lower the request response rate.

- If the backend is unavailable, the cache continues to manage requests by using the copy until the backend fails over to the backup data store.
- Caching is also an effective way of reducing network latency. Instead of reaching the central server, the response uses cache. That way, the client can receive the response instantaneously. If you need higher network performance and the ability to support more client connections, choose a higher tier of the caching service. However, higher tiers incur more costs.

## Caching can be expensive

Incorrect use of caching can result in severe business outcomes and higher costs.

- If you choose to add a cache, your architecture will have multiple data stores. There are added costs to keeping them coordinated. You might need to fill the cache before putting it in production. If you fill the cache on the application's first access, it can introduce latency. If you seed the cache, it can affect the application's start time. If you don't refresh the cache, your customers can get stale data.

    Invalidate the cache at the right time when there's latest information in the source system. Use strategies to age out the cache when appropriate.

- Add instrumentation to make sure the caching layer is working optimally. Instrumentation adds complexity and implementation cost.

Caching services such as Azure Cache for Redis offer tiers by cost. Pricing per tier is based  on the cache size and network performance. A smaller cache increases latency. Before you choose a tier, estimate a baseline. Try load testing the number of users and cache size.
