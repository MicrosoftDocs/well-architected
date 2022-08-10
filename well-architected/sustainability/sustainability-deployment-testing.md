---
title: Deployment and testing considerations for sustainable workloads on Azure
description: This design area explores deployment, testing and DevOps considerations for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - devops
products: Azure
ms.custom:
  - sustainability
---

# Deployment and testing considerations for sustainable workloads on Azure

Organizations usually develop and deploy solutions to the cloud. During this process, it's not uncommon to have various types of testing involved to ensure the workloads are of the highest quality. Learn about what considerations and recommendations there are for running workload tests, and how to optimize for a more sustainable testing model.

## Testing efficiency

### Design considerations

- Testing applications with unit testing, load testing, or any other intense testing capability may result in a lot of processing. A well-crafted design for testing the deployed workloads can help ensure a full utilization of the available resources, reducing the carbon emissions.

### Design recommendations

- Run unit-, load- or any other intense testing during low-carbon periods.
  - Opt for running testing when the energy mix of the data center is mostly using renewable energy. It may, for example, be more beneficial to run testing during night in some regions.

- Automate CI-CD to scale worker agents as needed.
  - Keeps the compute utilization high, based on the current demand, avoiding unnecessary capacity allocation.
  - Only scale out when it's necessary, and when not testing, scale in.
  - Consider optimized platform services like a container over testing in a VM, utilizing the platform to reduce maintenance.

## Profiling and measuring

Measuring, profiling and testing workloads is imperative to understanding how to best make use of allocated resources.

### Design considerations

- Without properly profiling and testing workloads, it's difficult to understand if it's making the best use of the underlying platform and deployed resources.

### Design recommendations

- Profile workloads to make use of parallelization where possible.
  - Test your applications to understand concurrent requests, simultaneous processing, and more.
  - If you're running Machine Learning (ML) for tests, consider machines with a GPU for better efficiency gains.

## Next step

Review the design considerations for operational procedures.

> [!div class="nextstepaction"]
> [Operational procedures](sustainability-operational-procedures.md)