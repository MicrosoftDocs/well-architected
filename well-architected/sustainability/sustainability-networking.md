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

Most workloads in the cloud rely heavily on networking to operate. Whether internal networking or public-facing workloads, the components and services used in provisioned solutions must consider the impact of carbon emissions. Consider that network equipment consumes electricity, including traffic between the data centers and end consumers. Learn about considerations and recommendations to enhance and optimize network efficiency to reduce unnecessary carbon emissions.

Internet traversal between data centers and end consumers is a significant [Scope 3 emission](sustainability-design-methodology.md#briefly-about-emission-scopes). Therefore, recommendations in this section are aligned with the Principles of Green Software [Networking](https://principles.green/principles/networking/) area to improve networking efficiency.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Network efficiency

Reduce unnecessary network traffic and lower bandwidth requirements where possible, allowing for a more optimized network efficiency with less carbon emission.

### Make use of a CDN

Unnecessary traffic on the network should be avoided, as it's a cause for extra carbon emissions.

_Green Software Foundation alignment: [Energy efficiency](sustainability-design-principles.md#energy-efficiency)_

**Recommendation:**

- A CDN helps minimize latency through storing frequently read static data closer to consumers, and helps reduce the network traversal and server load.
- Ensure to [follow best practices](/azure/architecture/best-practices/cdn) for CDN.

### Follow caching best practices

Minimizing the amount of data transferred is crucial.

_Green Software Foundation alignment: [Energy efficiency](sustainability-design-principles.md#energy-efficiency)_

**Recommendation:**

- Caching is a well-understood design technique to improve performance and efficiency.
- A caching solution helps reduce network traversal and reduces the server load.
- Consider that it may require tuning of parameters to maximize the benefit and minimize the carbon drawbacks. For example, setting a Time to Live (TTL).
- Adding in-memory caching can help use idle compute resources, increasing the compute density of resources that are already allocated.
- Read [caching best practices](/azure/architecture/best-practices/caching).

### Select Azure regions based on where the customer resides

The location of an application's consumers can be disparate, and it can be challenging to serve requests with good performance and energy efficiency if the distance is too great.

_Green Software Foundation alignment: [Energy efficiency](sustainability-design-principles.md#energy-efficiency)_

**Recommendation:**

- Deploy or [move Azure resources across regions](/azure/architecture/solution-ideas/articles/move-azure-resources-across-regions) to better serve the applications from where most consumers reside.

### Use managed audio and video streaming services with built-in compression

Applications making use of a media streaming service may have high requirements for bandwidth and compression, and can have a substantial carbon footprint if not designed carefully.

_Green Software Foundation alignment: [Hardware efficiency](sustainability-design-principles.md#hardware-efficiency)_

**Recommendation:**

- By making use of a managed service for audio and video, applications can leverage built-in optimizations like encoding, compressions, and more.
- Read about [managed audio and video streaming services](/azure/media-services/latest/encode-concept).

### Enable network file compression

Networks sending uncompressed data can have a higher requirement on bandwidth, the allocated resources, and the solution in general. Consider compressing data to optimize the workload and design for a more network efficient solution.

_Green Software Foundation alignment: [Energy efficiency](sustainability-design-principles.md#energy-efficiency)_

**Recommendation:**

- Reduce the network payload by [improving CDN performance](/azure/cdn/cdn-improve-performance).

### Maximize network utilization within the same cloud and region

Operating solutions in multiple regions have a networking impact. Network traversals between components in Azure are optimized to stay within the Azure infrastructure. However, any network traffic destined for the internet or a component in another cloud involves the public internet's router resources, which you have no control over regarding resource impact measurement or utilization.

_Green Software Foundation alignment: [Energy efficiency](sustainability-design-principles.md#energy-efficiency)_

**Recommendation:**

- Keeping resources in a single cloud gives you maximum control and allows the cloud provider to optimize the network routing.
- Maximize network utilization within the same cloud and, if possible, within the same region.
- Since the cost can be a proxy for sustainability, review the [Azure regions](/azure/architecture/framework/cost/design-regions) documentation in the Cost Optimization pillar of the Azure Well-Architected Framework.

## Next step

Review the design considerations for storage.

> [!div class="nextstepaction"]
> [Storage](sustainability-storage.md)
