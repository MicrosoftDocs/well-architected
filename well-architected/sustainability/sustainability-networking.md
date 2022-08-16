---
title: Networking considerations for sustainable workloads on Azure
description: This design area explores networking considerations for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Networking considerations for sustainable workloads on Azure

Most workloads in the cloud rely heavily on networking to operate. Whether it's internal networking, or public facing workloads, the components and services used in provisioned solutions should be designed with sustainability in mind. Consider that network equipment consumes electicity, including traffic between the data centers and end consumer. Learn about considerations and recommendations to enhance and optimize the network efficiency to reduce unnecessary carbon emissions.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Network efficiency

Reduce unnecessary network traffic and lower bandwidth requirements where possible, allowing for a more optimized network efficiency with less carbon emission.

### Design considerations

- Unnecessary traffic on the network should be avoided, as it's a cause for additional carbon emissions.
- Avoid large amounts of data to be transferred if there are ways to prevent it.
- The location of an application's consumers can be disparate, and it can be challenging to serve requests with good performance and energy efficiency if the distance is too great.
- Applications making use of a media streaming service may have high requirements for bandwidth and compression, and can have a substantial carbon footprint if not designed carefully.
- Networks sending uncompressed data can have a significantly higher requirement on bandwidth, the allocated resources, and the solution in general. Consider compressing data to optimize the workload and design for a more network efficient solution.

### Design recommendations

- Make use of a CDN, and [follow best practices](/azure/architecture/best-practices/cdn).
  - A CDN helps minimize latency through storing frequently read static data closer to consumers, and helps reduce the network traversal and server load.

- Make use of caching and follow [caching best practices](/azure/architecture/best-practices/caching).
  - Caching is a well-understood design technique to improve performance and efficiency.
  - A caching solution helps reduce network traversal and reduces the server load.
  - Consider that it may require tuning of parameters to maximize the benefit and minimize the carbon drawbacks. For example, setting a Time to Live (TTL).
  - Adding in-memory caching can help use idle compute resources, increasing the compute density of resources that are already allocated.

- Select Azure regions based on where the consumer resides.
  - Deploy or [move Azure resources across regions](/azure/architecture/solution-ideas/articles/move-azure-resources-across-regions) to better serve the applications from where the majority of consumers resides.

- Use [managed audio and video streaming services](/azure/media-services/latest/encode-concept) that use built-in compression.
  - By making use of a managed service for audio and video, applications can leverage built-in optimizations like encoding, compressions, and more.

- Enable network compression.
  - Reduce the network payload by [improving CDN performance](/azure/cdn/cdn-improve-performance).

## Next step

Review the design considerations for storage.

> [!div class="nextstepaction"]
> [Storage](sustainability-storage.md)
