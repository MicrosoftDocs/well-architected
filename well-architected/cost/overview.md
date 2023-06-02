---
title: Overview of the cost optimization pillar
description: Learn about the cost optimization pillar in the Azure Well-Architected Framework for balancing business goals with operations costs.
ms.author: martinek
author: martinekuan
ms.date: 03/29/2023
ms.topic: conceptual
ms.custom:
  - overview
  - internal-intro
categories:
  - management-and-governance
---

# Overview of the cost optimization pillar

The cost optimization pillar provides principles for balancing business goals with budget justification. The principles help you create a cost-effective workload while avoiding capital-intensive solutions. Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies.

Use the pay-as-you-go strategy for your architecture, and invest in [scaling out](optimize-autoscale.md), rather than delivering a large investment-first version. Consider opportunity costs in your architecture, and the balance between *first mover* advantage versus *fast follow*. Use the [cost calculators](https://azure.microsoft.com/pricing/calculator/) to estimate the initial cost and operation costs. Finally, establish [policies](principles.md), [budgets, and controls](monitor-alert.md) that set cost limits for your solution.

To assess your workload using the tenets found in the [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/), see the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

We recommend exploring the following videos to dive deeper into Azure cost optimization:

<!-- markdownlint-disable MD034 -->

<br/>

> [!VIDEO https://learn.microsoft.com/shows/Azure-Enablement/Diving-deeper-into-Azure-cost-optimization-Part-1-Cost-Optimization-Ep-2-Well-Architected-series/player]
<br>

> [!VIDEO https://learn.microsoft.com/shows/Azure-Enablement/Diving-deeper-into-Azure-cost-optimization-Part-2-Cost-Optimization-Ep-2-Well-Architected-series/player]

<!-- markdownlint-enable MD034 -->

## Azure Well-Architected Framework cost optimization articles

The Azure Well-Architected Framework includes the following articles in the cost optimization pillar:

| Cost area | Description |
|------------|-------------|
|[Capture cost requirements](./design-capture-requirements.md)| Start your planning with a careful enumeration of requirements. Make sure the needs of the stakeholders are addressed. For strong alignment with business goals, the stakeholders must define the needs not the vendors.|
|[Cost of resources in Azure regions](./design-regions.md)|Cost of an Azure service can vary between locations based on demand and local infrastructure costs.|
|[Governance](./design-governance.md)| Understand how governance can assist with cost management. This work benefits your ongoing cost review process and offers a level of protection for new resources.|
|[Estimate the initial cost](./design-initial-estimate.md)|It's difficult to attribute costs before deploying a workload to the cloud. If you use methods for on-premises estimation or directly map on-premises assets to cloud resources, the estimate is inaccurate.|
|[PaaS](./design-paas.md)| Look for areas in the architecture where it might be natural to incorporate platform-as-a-service (PaaS) options. These options include caching, queues, and data storage. PaaS reduces time and cost of managing servers, storage, networking, and other application infrastructure.|
|[Consumption](./design-price.md)|A common way to estimate cost is by considering workloads on a peak throughput. Under consistently high usage, consumption-based pricing can be less efficient for estimating baseline costs when compared to the equivalent provisioned pricing.|
|[Provision cloud resources](./provision-checklist.md)|Deployment of workload cloud resources can optimize cost.|
|[Monitor cost](./monitor-alert.md)|Azure Cost Management has an alert feature. Alerts are generated when consumption reaches a threshold.|
|[Optimize cost](./optimize-checklist.md)|Monitor and optimize the workload by using the right resources and sizes.
|[Tradeoffs for costs](./tradeoffs.md)|As you design the workload, consider tradeoffs between cost optimization and other aspects of the design, such as security, scalability, resilience, and operability.

## Next section

Use the cost optimization principles to guide you in your overall strategy.

> [!div class="nextstepaction"]
> [Principles](principles.md)
