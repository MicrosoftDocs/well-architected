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

Designing and building sustainable workloads requires understanding the platform where you are deploying the applications. Review the considerations and recommendations in this section to know how to make better informed platform-related decisions around sustainability.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Stay updated

### Design considerations

- Running applications on outdated versions of a service, OS, or software library may result in unnecessary performance issues and, ultimately, resolve energy waste with a newer version.

### Design recommendations

- Update services, OS, and software libraries.
  - New software tends to be more efficient in general. Performance gains often come with new upgrades and updates.
  - Consider backward compatibility and hardware reusability. An upgrade may not be the most efficient solution if the hardware or the OS is not supported.

## Regional differences

The Microsoft Azure data centers are geographically spread across the planet and powered using different energy sources. Making decisions around where to deploy your workloads can significantly impact the emissions your solutions produce.

### Design considerations

- Learn about what Azure regions have a lower carbon footprint than others to make better-informed decisions about where and how our workloads process data.
  - Some regions on the planet are more carbon intense than others, so it's important to consider where we deploy our workloads and combine this with other business requirements.

### Design recommendations

- Deploy to low-carbon regions and process when carbon intensity is low.
  - Use less carbon because the data centers where you deploy the workload are more likely to be powered by renewable and low-carbon energy sources.
  - Potential tradeoffs:
    - The effort and time it takes to move to a low-carbon region.
    - Migrating data between data centers may not be carbon efficient.
    - Consider the cost for new regions, including low-carbon regions, which may be more expensive.

- Process when the carbon intensity is low.
  - Optimizing workloads to run when knowing that the energy mix comes mostly from renewable energy sources.
  - For example, running specific workloads at night may be more beneficial in some regions.

## Next step

Review the design considerations for deployment and testing.

> [!div class="nextstepaction"]
> [Deployment and testing](sustainability-deployment-testing.md)
