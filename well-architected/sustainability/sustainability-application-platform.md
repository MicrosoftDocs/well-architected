---
title: Application platform considerations for sustainable workloads on Azure
description: This design area explores application platform and infrastructure considerations for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/29/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Application platform considerations for sustainable workloads on Azure

[Intro paragraph]

## Stay updated

### Design considerations

- Running applications on outdated versions of a service, OS or software library may result in unnecessary performance issues, and ultimately energy waste, that could be resolved with a newer version.

### Design recommendations

- Update services, OS, and software libraries.
  - New software tends to be more efficient in general. Performance gains often comes with new upgrades and updates.
  - Consider backwards-compatibility and hardware reusability. If the hardware or the OS is not supported, an upgrade may not be the most efficient solution right now.

## Regional differences

The Microsoft Azure datacenters are geographically spread out across the planet, and are powered using different energy sources. Making decisions around where to deploy your workloads can significantly impact the emissions your solutions produce.

### Design considerations

- Learn about what Azure regions have a lower carbon footprint than other to better make informed decisions about where and how our workloads process data.
  - Some regions on the planet are more carbon intense than others, and therefore its important to consider where we deploy our workloads, and combine this with other business requirements.

### Design recommendations

- Deploy to low-carbon regions and process when carbon intensity is low.
  - Use less carbon because the data centers where you deployed the workload is more likely to be powered by renewable energy and low-carbon energy sources.
  - Potential tradeoffs:
    - The effort and time it takes to move to a low-carbon region.
    - Migrating data between datacenters may not be carbon efficient.
    - Consider the cost for new regions, including low-carbon regions, which may be more expensive.

- Process when the carbon intensity is low.
  - Optimizing workloads to run when knowing that the energy mix comes mostly from renewable energy sources.
  - For example, it may be more beneficial to run certain workloads during the night in some regions.

## Next step

Review the design considerations for deployment and testing.

> [!div class="nextstepaction"]
> [Deployment and testing](sustainability-deployment-testing.md)