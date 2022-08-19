---
title: Testing considerations for sustainable workloads on Azure
description: This design area explores testing and DevOps considerations for sustainable workloads on Azure.
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

# Testing considerations for sustainable workloads on Azure

Organizations developing and deploying solutions to the cloud also need reliable testing. Learn about the considerations and recommendations for running workload tests and how to optimize for a more sustainable testing model.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Testing efficiency

### Design considerations

- Testing applications with unit testing, load testing, or any other intense testing capability may result in much processing. A well-crafted design for testing the deployed workloads can help ensure full utilization of the available resources, reducing carbon emissions.

### Design recommendations

- Run unit, load or any other intense testing during low-carbon periods.
  - Opt for running testing when the data center's energy mix primarily uses renewable energy. It may, for example, be more beneficial to run testing during the night in some regions.

- Automate CI/CD to scale worker agents as needed.
  - Keeps the compute utilization high, based on the current demand, avoiding unnecessary capacity allocation.
  - Only scale out when necessary, and when not testing, scale in. Ultimately this ensures there's no idle compute resources in test environments.
  - Consider optimized platform services like containers over testing in a VM, utilizing the platform to reduce maintenance.

## Profiling and measuring

Measuring, profiling, and testing workloads are imperative to understanding how to best use allocated resources.

### Design considerations

- Without properly profiling and testing workloads, it's difficult to know if it's making the best use of the underlying platform and deployed resources.

- Running unit and load tests increases the reliability of a workload. However, the introduction of chaos engineering can greatly help improve the reliability and resilience, and how the application react to failures. In doing so, the workload can be optimized to handle failures more gracefully and with less wasted resources.

### Design recommendations

- Profile workloads to make use of parallelization where possible.
  - Test your applications to understand concurrent requests, simultaneous processing, and more.
  - If you're running Machine Learning (ML) for tests, consider machines with a GPU for better efficiency gains.
    - Consider this tradeoff: Running GPU-based machines for ML tests may increase the cost.
  - Identify if the workload is performance intensive and work toward optimization.

- Use load testing or [chaos engineering](/azure/architecture/framework/resiliency/chaos-engineering) to assess how the workload handles platform outages, and traffic spikes or dips.
  - Increase the service resilience and the ability to react to failures, allowing for a more optimized fault handling.
  - Consider this tradeoff: Injecting fault during chaos engineering and increasing the load on any system also increases the emissions used for the testing resources. Evaluate how and when you can utilize chaos engineering to increase the workload reliability, while also considering the climate impact of running unnecessary testing sessions.

## Next step

Review the design considerations for operational procedures.

> [!div class="nextstepaction"]
> [Operational procedures](sustainability-operational-procedures.md)
