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

- Testing applications with unit testing, load testing, or any other intense testing capability may result in a lot of processing. A well-crafted design for testing the deployed workloads can help reduce the carbon emissions.

### Design recommendations

- Run unit-, load- or any other intense testing during low-carbon periods.
  - Opt for running testing when the energy mix of the data center is mostly using renewable energy. It may, for example, be more beneficial to run testing during night in some regions.

## Assess your workloads

### Design considerations

### Design recommendations

|Design principle|WAF Pillar|GSF Pillar|Considerations|
|---|---|---|---|
|Use load testing or chaos engineering to assess how platform outages and/or spikes/dips in load affect the workload's sustainability|Operational Excellence|Measure Sustainability|TBD|
|Use methods like A/B testing (or equivalent) to assess the impact of live user data on the sustainability of the workload whenever the code or architecture is updated|Operational Excellence|Measure Sustainability|TBD|

## Improve efficiency

### Design considerations

### Design recommendations

|Design principle|WAF Pillar|GSF Pillar|Considerations|
|---|---|---|---|
|Unit test during low carbon periods|Performance Efficiency|Carbon Awareness|TBD|
|Profile workloads to make use of parallelization where possible with GPUs vs CPUs for efficiency gains (for example, inference). Profile workloads to get balance of CPU and memory usage.|Performance Efficiency|Energy Efficiency|TBD|
|Leverage repeatable/automated practices to deploy, operate and maintain your solution to maximize environmental efficiency|Operational Excellence|Carbon Efficiency|TBD|
|Automate CI/CD to scale worker agents as needed|Performance Efficiency|Hardware Efficiency|TBD|

## Next step

Review the design considerations for operational procedures.

> [!div class="nextstepaction"]
> [Operational procedures](sustainability-operational-procedures.md)