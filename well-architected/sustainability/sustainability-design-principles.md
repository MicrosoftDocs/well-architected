---
title: Design principles for sustainability workloads on Azure
description: Understand the design principles for building a sustainable application on Microsoft Azure.
author: Zimmergren
ms.author: tozimmergren
ms.date: 07/22/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - management-and-governance
  - web
products: Azure
ms.custom:
  - sustainability
---

# Design principles of a sustainable workload

The sustainability design methodology provides a framework to record, report and reduce the environmental impact of your workloads.

To achieve an increase in carbon efficiency, consider how your workload directly, and indirectly, can reduce carbon emissions through:

- Using less physical and virtual resources
- Using less energy
- Using energy and resources more intelligently
- Supporting older devices

It's important to effectively record, report, and reduce carbon emissions through actionable insights.

- Gain transparency into your current carbon impact
- Estimate savings
- Take action to accelerate progress

These critical design principles for sustainability resonate and extend the quality pillars of the Azure Well-Architected Framework&mdash;[Reliability](/azure/architecture/framework/#reliability), [Security](/azure/architecture/framework/security/), [Cost Optimization](/azure/architecture/framework/cost/), [Operational Excellence](/azure/architecture/framework/devops/), and [Performance Efficiency](/azure/architecture/framework/scalability/).

## Principles of green software

The principles of green software are the starting point to understand the SCI model and how this will be included in our framework.

Each design principle map to one or more of the pillars in the Azure Well-Architected Framework.

## Carbon Efficient

The application or software must emit the least amount of carbon possible. A carbon efficient cloud application is one that is optimized, and the starting point is the cost – streamlining the application infrastructure and cost will ensure that no unnecessary resources are wasted in the cloud to run the software. But this is not enough, as you might have cost optimized your application but still waste tons of resources that emit carbon for no reason.

|Design principle|Pillar|Considerations|
|---|---|---|
|Deploy low-carbon regions and process when carbon intensity is low|Performance Efficiency|TBD.|
|Leverage repeatable/automated practices to deploy, operate and maintain your solution to maximize environmental efficiency|Operational Excellence|TBD|
|Allow team members to share their workload and company-specific best practices for sustainable operations|Operational Excellence|TBD|

## Energy efficiency

The goal of this principle is that you build applications that are energy-efficient. This is a common pattern for mobile applications, since they must rely on a battery powered device and are optimizing its consumption. It's less common, however, for desktop or web applications, since until now, developers have never been asked to optimize the electricity consumption of their software.

|Design principle|Pillar|Considerations|
|---|---|---|
|[Update services, OS, software libraries to gain performance efficiencies](/azure/architecture/hybrid/azure-update-mgmt)|Performance Efficiency|TBD|
|Consider ARM SKUs for VMs/compute|Performance Efficiency|TBD|
|Profile workloads to make use of parallelization where possible with GPUs vs CPUs for efficiency gains (for example, inference). Profile workloads to get balance of CPU and memory usage.|Performance Efficiency|TBD|
|[Select regions based on where the consumer resides](/azure/architecture/solution-ideas/articles/move-azure-resources-across-regions)|Performance Efficiency|TBD|
|[Consider CDN and/or enable local caching](/azure/architecture/best-practices/cdn)|Performance Efficiency|TBD|
|Enable network compression. Reduce network payload|Performance Efficiency|TBD|
|[Use storage best suited to data access patterns](/azure/architecture/guide/design-principles/use-best-data-store)|Performance Efficiency|TBD|
|Be aware of UX design for sustainability, e.g.,  use SD rather than HD|Performance Efficiency|TBD|
|Keep OS, language runtimes and libraries up to date for latest efficiency gains|Operational Excellence|TBD|
|Review platform updates regularly, and upgrade to newer/more efficient services as they become available|Operational Excellence|TBD|
|[Restartability - amount of processing to recover from failure](/azure/backup/manage-recovery-points#impact-of-expired-recovery-points-for-items-in-soft-deleted-state)|Reliability|TBD|
|Circuit breaker patterns - if a service isn't available, don't continue to ping it/retry it|Reliability|TBD|
|Power off workloads out of hours (such as Dev/Test workloads)|Cost Optimization|TBD|

## Carbon Awareness

We need to make the application aware of how much carbon it’s emitting. This way, we can react to specific conditions of energy supply, using demand shifting and demand shaping techniques:

- Demand shifting means moving the workloads and resources to regions or datacenters where the energy supply is higher (and incidentally the cost is lower)
- Demand shaping means changing the application’s behavior and appearance to match the energy supply in real-time. A good practice is to build an eco-version of the app and keep it as benchmark for demand shaping but also for carbon optimization.

|Design principle|Pillar|Considerations|
|---|---|---|
|Unit test during low carbon periods|Performance Efficiency|TBD|
|[Demand shaping - Run batch workloads during low carbon intensity periods](/azure/architecture/data-guide/big-data/batch-processing)|Performance Efficiency|TBD|

## Hardware Efficiency

Embodied carbon is the carbon that was emitted to build a device. Therefore, a sustainable application will make sure older devices are supported and will maximize the efficiency of each device.  The goal is to build hardware-efficient applications.

|Design principle|Pillar|Considerations|
|---|---|---|
|[Autoscale components to optimize utilization](/azure/architecture/best-practices/auto-scaling)|Performance Efficiency|TBD|
|Automate CI/CD to scale worker agents as needed|Performance Efficiency|TBD|
|[Use managed video streaming services that use built-in compression](/azure/media-services/latest/encode-concept)|Performance Efficiency|TBD|
|[Enable storage compression](/azure/cdn/cdn-improve-performance)|Performance Efficiency|TBD|
|[Store only what is relevant. Implement policies to streamline the process](/azure/architecture/guide/design-principles/use-best-data-store)|Performance Efficiency|TBD|
|Ensure software backwards compatibility so it works on legacy hardware|Performance Efficiency|TBD|
|Keep TTL low to avoid idle resource|Performance Efficiency|TBD|
|Update legacy code, identify inefficient legacy code for modernization, make use of serverless, PaaS|Operational Excellence|TBD|
|Move to the cloud! Moving your VMs to the cloud will reduce carbon impact, modernization will further improve|Operational Excellence|TBD|
|Use automation to automatically shut down infrastructure or resources that aren't needed to minimize environmental impact of idle resources|Operational Excellence|TBD|
|Classify data and move stale data to cold storage; delete data no longer needed|Operational Excellence|TBD|
|Smaller failure units|Reliability|TBD|
|Is running active/active a sustainability improvement (reduced network resources from CDNs, smaller failure units, less 'wasted' compute in booting/testing resources in passive regions|Reliability|TBD|
|[Reducing number of recovery points in place](/azure/backup/manage-recovery-points#impact-of-expired-recovery-points-for-items-in-soft-deleted-state)|Reliability|TBD|
|Correct sizing of Azure resources|Cost Optimization|TBD|
|Maximize the workloads on compute|Cost Optimization|TBD|
|Reduce compute - resizing, moving to serverless, rightsizing, spot instances|Cost Optimization|TBD|
|Move to serverless|Cost Optimization|TBD|
|Use spot VMs|Cost Optimization|TBD|
|Use PaaS over IaaS where possible|Cost Optimization|TBD|
|Delete zombie workloads|Cost Optimization|TBD|
|Reduce copies of data or backups where possible|Cost Optimization|TBD|

## Measuring Sustainability

Measuring carbon emissions of a cloud application is a complex task, as it involves the whole ecosystem of the software: from the cloud infrastructure (where we have the emissions dashboards to help us out), to the network path that is crossed, to the edge technology and user devices. With the SCI, we aren't targeting a discrete measurement of carbon emissions, but a score that will change over time and with our optimization techniques.

|Design principle|Pillar|Considerations|
|---|---|---|
|Identify metrics that you can track and quantify the achievement of technical/business and sustainability outcomes|Operational Excellence|TBD|
|Use the Emissions Impact Dashboard to record current and future impact|Operational Excellence|TBD|
|Use load testing or chaos engineering to access how platform outages and/or spikes/dips in load affect the workload's sustainability|Operational Excellence|TBD|
|Define emission targets when designing the workload and track progress against them just like with SLO/SLA figures or other performance metrics|Operational Excellence|TBD|
|Use methods like A/B testing (or equivalent) to assess the impact of live user data on the sustainability of the workload whenever the code or architecture is updated|Operational Excellence|TBD|
|Identify the metrics and set improvement goals from a sustainability perspective (Compute/Network/Storage)|
|Have a sustainability review|Operational Excellence|TBD|
|Cost optimization can be a proxy for Sustainability|Cost Optimization|TBD|

## Climate Commitments



|Design principle|Pillar|Considerations|
|---|---|---|
|Make time for your team to learn about advancements in sustainable operations|Operational Excellence|TBD|
|Consider defining policies for minimum emission standards of any additional/changes that are made to the workload|Operational Excellence|TBD.|
|Create a sustainability community|Operational Excellence|TBD|
|Create incentives for improving the environmental sustainability of a workload that aligns with the incentives you would award for a performance or cost improvement|Operational Excellence|TBD|


## Next steps

Review cross-cutting concerns associated with sustainable workloads.

> [!div class="nextstepaction"]
> [Cross-cutting concerns](sustainability-cross-cutting-concerns.md)